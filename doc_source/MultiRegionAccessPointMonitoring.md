# Monitoring and logging requests made through a Multi\-Region Access Point to underlying resources<a name="MultiRegionAccessPointMonitoring"></a>

 Amazon S3 logs requests made through Multi\-Region Access Point and requests made to the APIs that manage them, such as `CreateMultiRegionAccessPoint` and `GetMultiRegionAccessPointPolicy`\. Requests made to Amazon S3 through a Multi\-Region Access Point appear in your Amazon S3 server access logs and AWS CloudTrail logs with the Multi\-Region Access Point hostname\. An access point's hostname takes the form `<MRAP_alias>.accesspoint.s3-global.amazonaws.com`\. For example, suppose that you have the following bucket and Multi\-Region Access Point configuration: 
+  A bucket named `my-bucket-usw2` in Region `us-west-2` that contains object `my-image.jpg`\. 
+  A bucket named `my-bucket-aps1` in Region `ap-south-1` that contains object `my-image.jpg`\. 
+  A bucket named `my-bucket-euc1` in Region `eu-central-1` that doesn’t contain an object named `my-image.jpg`\. 
+  A Multi\-Region Access Point named `my-mrap` with the alias `mfzwi23gnjvgw.mrap` that is configured to fulfill requests from all three buckets\. 
+  Your AWS account ID is `123456789012`\. 

 A request made to retrieve `my-image.jpg` directly through any of the buckets appears in your logs with a hostname of `<bucket_name>.s3.<Region>.amazonaws.com`\. 

 If you make the request through the Multi\-Region Access Point instead, Amazon S3 first determines which of the buckets in the different Regions is of closest proximity\. After Amazon S3 determines which bucket to use to fulfill the request, it sends the request to that bucket and logs the operation using the Multi\-Region Access Point hostname\. In this example, if Amazon S3 relayed the request to `my-bucket-aps1`, your logs would reflect a successful `GET` request for `my-image.jpg` from `my-bucket-aps1`, using a hostname of `mfzwi23gnjvgw.mrap.accesspoint.s3-global.amazonaws.com`\. 

 It is important to be aware that Amazon S3 does not perform any consideration about which bucket might be able to fulfill the request\. If Amazon S3 determined that the `my-bucket-euc1` bucket is of closest proximity, your logs would reflect a failed `GET` request for `my-image.jpg` from `my-bucket-euc1`, using a hostname of `mfzwi23gnjvgw.mrap.accesspoint.s3-global.amazonaws.com`\. If the request had been routed to `my-bucket-usw2` instead, your logs would indicate a successful `GET` request\.

 For more information about Amazon S3 server access logs, see [Logging requests using server access logging](ServerLogs.md)\. For more information about AWS CloudTrail, see [What Is AWS CloudTrail?](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html) in the *AWS CloudTrail User Guide*\. 

## Monitoring and logging requests made to Multi\-Region Access Point management APIs<a name="MonitoringMultiRegionAccessPointAPIs"></a>

 Amazon S3 provides several operations to manage Multi\-Region Access Points, such as `CreateMultiRegionAccessPoint` and `GetMultiRegionAccessPointPolicy`\. When you make these requests using the AWS CLI, SDKs, or REST API, Amazon S3 processes these requests asynchronously\. Provided that you have the appropriate permissions for the request, Amazon S3 returns a token for these requests\. You can use this token with `DescribeAsyncOperation` to help you to view the status of ongoing asynchronous operations\. S3 processes `DescribeAsyncOperation` requests synchronously\. You can use the AWS Management Console, AWS CLI, SDKs, or REST API to view the status of asynchronous requests\. 

**Note**  
 The console only displays the status of asynchronous requests made within the previous 14 days\. To view the status of older requests, use the AWS CLI, SDKs, or REST API\. 

 Asynchronous management operations can be in one of several states: 

NEW  
 Amazon S3 has received the request and is preparing to perform the operation\. 

IN\_PROGRESS  
 Amazon S3 is currently performing the operation\. 

SUCCESS  
 The operation succeeded\. The response includes relevant information, such as the Multi\-Region Access Point alias for a `CreateMultiRegionAccessPoint` request\. 

FAILED  
 The operation failed\. The response includes an error message indicating the reason for the request failure\. 

**Topics**
+ [Monitoring and logging requests made to Multi\-Region Access Point management APIs](#MonitoringMultiRegionAccessPointAPIs)
+ [AWS CloudTrail with Multi\-Region Access Points](MultiRegionAccessPointCloudTrail.md)