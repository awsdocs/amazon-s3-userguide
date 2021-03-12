--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Monitoring and logging access points<a name="access-points-monitoring-logging"></a>

Amazon S3 logs requests made through access points and requests made to the APIs that manage access points, such as `CreateAccessPoint` and `GetAccessPointPolicy`\.

Requests made to Amazon S3 through an access point appear in your S3 server access logs and AWS CloudTrail logs with the access point's hostname\. An access point's hostname takes the form `access_point_name-account_id.s3-accesspoint.Region.amazonaws.com`\. For example, suppose that you have the following bucket and access point configuration:
+ A bucket named `my-bucket` in Region `us-west-2` that contains object `my-image.jpg`
+ An access point named `my-bucket-ap` that is associated with `my-bucket`
+ Your AWS account ID is `123456789012`

A request made to retrieve `my-image.jpg` directly through the bucket appears in your logs with a hostname of `my-bucket.s3.us-west-2.amazonaws.com`\. If you make the request through the access point instead, Amazon S3 retrieves the same object but logs the request with a hostname of `my-bucket-ap-123456789012.s3-accesspoint.us-west-2.amazonaws.com`\.

For more information about S3 Server Access Logs, see [Logging requests using server access logging](ServerLogs.md)\. For more information about AWS CloudTrail, see [What is AWS CloudTrail?](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html) in the *AWS CloudTrail User Guide*\.

**Note**  
S3 access points aren't currently compatible with Amazon CloudWatch metrics\.