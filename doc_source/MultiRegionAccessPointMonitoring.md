# Monitoring and logging requests made through a Multi\-Region Access Point to underlying resources<a name="MultiRegionAccessPointMonitoring"></a>

Amazon S3 logs requests made through Multi\-Region Access Points and requests made to the API operations that manage them, such as `CreateMultiRegionAccessPoint` and `GetMultiRegionAccessPointPolicy`\. Requests made to Amazon S3 through a Multi\-Region Access Point appear in your Amazon S3 server access logs and AWS CloudTrail logs with the Multi\-Region Access Point hostname\. An access point's hostname takes the form `MRAP_alias.accesspoint.s3-global.amazonaws.com`\. For example, suppose that you have the following bucket and Multi\-Region Access Point configuration: 
+ A bucket named `my-bucket-usw2` in the Region `us-west-2` that contains the object `my-image.jpg`\. 
+ A bucket named `my-bucket-aps1` in the Region `ap-south-1` that contains the object `my-image.jpg`\. 
+  A bucket named `my-bucket-euc1` in the Region `eu-central-1` that doesn’t contain an object named `my-image.jpg`\. 
+  A Multi\-Region Access Point named `my-mrap` with the alias `mfzwi23gnjvgw.mrap` that is configured to fulfill requests from all three buckets\. 
+  Your AWS account ID is `123456789012`\. 

A request made to retrieve `my-image.jpg` directly through any of the buckets appears in your logs with a hostname of `bucket_name.s3.Region.amazonaws.com`\. 

If you make the request through the Multi\-Region Access Point instead, Amazon S3 first determines which of the buckets in the different Regions is closest\. After Amazon S3 determines which bucket to use to fulfill the request, it sends the request to that bucket and logs the operation by using the Multi\-Region Access Point hostname\. In this example, if Amazon S3 relays the request to `my-bucket-aps1`, your logs will reflect a successful `GET` request for `my-image.jpg` from `my-bucket-aps1`, using a hostname of `mfzwi23gnjvgw.mrap.accesspoint.s3-global.amazonaws.com`\. 

**Important**  
Multi\-Region Access Points aren't aware of the data contents of the underlying buckets\. Therefore, the bucket that gets the request might not contain the requested data\. For example, if Amazon S3 determines that the `my-bucket-euc1` bucket is the closest, your logs will reflect a failed `GET` request for `my-image.jpg` from `my-bucket-euc1`, using a hostname of `mfzwi23gnjvgw.mrap.accesspoint.s3-global.amazonaws.com`\. If the request was routed to `my-bucket-usw2` instead, your logs would indicate a successful `GET` request\.

 For more information about Amazon S3 server access logs, see [Logging requests using server access logging](ServerLogs.md)\. For more information about AWS CloudTrail, see [What is AWS CloudTrail?](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html) in the *AWS CloudTrail User Guide*\. 

## Monitoring and logging requests made to Multi\-Region Access Point management API operations<a name="MonitoringMultiRegionAccessPointAPIs"></a>

Amazon S3 provides several API operations to manage Multi\-Region Access Points, such as `CreateMultiRegionAccessPoint` and `GetMultiRegionAccessPointPolicy`\. When you make requests to these API operations by using the AWS Command Line Interface \(AWS CLI\), AWS SDKs, or Amazon S3 REST API, Amazon S3 processes these requests asynchronously\. Provided that you have the appropriate permissions for the request, Amazon S3 returns a token for these requests\. You can use this token with `DescribeAsyncOperation` to help you to view the status of ongoing asynchronous operations\. Amazon S3 processes `DescribeAsyncOperation` requests synchronously\. To view the status of asynchronous requests, you can use the Amazon S3 console, AWS CLI, SDKs, or REST API\. 

**Note**  
The console displays only the status of asynchronous requests that were made within the previous 14 days\. To view the status of older requests, use the AWS CLI, SDKs, or REST API\. 

 Asynchronous management operations can be in one of several states: 

NEW  
 Amazon S3 has received the request and is preparing to perform the operation\. 

IN\_PROGRESS  
 Amazon S3 is currently performing the operation\. 

SUCCESS  
 The operation succeeded\. The response includes relevant information, such as the Multi\-Region Access Point alias for a `CreateMultiRegionAccessPoint` request\. 

FAILED  
 The operation failed\. The response includes an error message that indicates the reason for the request failure\. 

## Using AWS CloudTrail with Multi\-Region Access Points<a name="MultiRegionAccessPointCloudTrail"></a>

You can use AWS CloudTrail to view, search, download, archive, analyze, and respond to account activity across your AWS infrastructure\. With Multi\-Region Access Points and CloudTrail logging, you can identify the following: 
+ Who or what took which action
+ Which resources were acted upon
+ When the event occurred
+ Other details regarding the event

You can use this logging information to help you analyze and respond to activity that occurred through your Multi\-Region Access Points\. 

### How to set up AWS CloudTrail for Multi\-Region Access Points<a name="MultiRegionAccessPointCTSetup"></a>

To enable CloudTrail logging for any operations related to creating or maintaining Multi\-Region Access Points, you must configure CloudTrail logging to record the events in the US West \(Oregon\) Region\. You must set up your logging configuration this way regardless of which Region you are in when making the request, or which Regions the Multi\-Region Access Point supports\. All requests to create or maintain a Multi\-Region Access Point are routed through the US West \(Oregon\) Region\. We recommend that you either add this Region to an existing trail or create a new trail that contains this Region and all the Regions associated with the Multi\-Region Access Point\.

Amazon S3 logs all requests made through a Multi\-Region Access Point and requests made to the API operations that manage access points, such as `CreateMultiRegionAccessPoint` and `GetMultiRegionAccessPointPolicy`\. When you log these requests through a Multi\-Region Access Point, they appear in your AWS CloudTrail logs with the hostname of the Multi\-Region Access Point\. For example, if you make requests to a bucket through a Multi\-Region Access Point with the alias `mfzwi23gnjvgw.mrap`, the entries in the CloudTrail log will have a hostname of `mfzwi23gnjvgw.mrap.accesspoint.s3-global.amazonaws.com`\. 

Multi\-Region Access Points route requests to the closest bucketBecause of this behavior, when you are looking at the CloudTrail logs for a Multi\-Region Access Point, you will see requests being made to the underlying buckets\. Some of those requests might be direct requests to the bucket that are not routed through the Multi\-Region Access Point\. Keep this fact in mind when reviewing traffic\. When a bucket is in a Multi\-Region Access Point, requests can still be made to that bucket directly without going through the Multi\-Region Access Point\. 

There are asynchronous events involved with creating and managing Multi\-Region Access Points\. Asynchronous requests don't have completion events in the CloudTrail log\. For more information about asynchronous requests, see [Monitoring and logging requests made to Multi\-Region Access Point management API operations](#MonitoringMultiRegionAccessPointAPIs)\. 

 For more information about AWS CloudTrail, see [What is AWS CloudTrail?](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html) in the *AWS CloudTrail User Guide*\. 