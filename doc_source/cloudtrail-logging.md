# Logging Amazon S3 API calls using AWS CloudTrail<a name="cloudtrail-logging"></a>

Amazon S3 is integrated with AWS CloudTrail, a service that provides a record of actions taken by a user, role, or an AWS service in Amazon S3\. CloudTrail captures a subset of API calls for Amazon S3 as events, including calls from the Amazon S3 console and code calls to the Amazon S3 APIs\.

If you create a trail, you can enable continuous delivery of CloudTrail events to an Amazon S3 bucket, including events for Amazon S3\. If you don't configure a trail, you can still view the most recent events in the CloudTrail console in **Event history**\. Using the information collected by CloudTrail, you can determine the request that was made to Amazon S3, the IP address from which the request was made, who made the request, when it was made, and additional details\. 

To learn more about CloudTrail, including how to configure and enable it, see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\.

## Using CloudTrail logs with Amazon S3 server access logs and CloudWatch Logs<a name="cloudtrail-logging-vs-server-logs"></a>

AWS CloudTrail logs provide a record of actions taken by a user, role, or an AWS service in Amazon S3, while Amazon S3 server access logs provide detailed records for the requests that are made to an S3 bucket\. For more information about how the different logs work, and their properties, performance, and costs, see [Logging options for Amazon S3](logging-with-S3.md)\. 

You can use AWS CloudTrail logs together with server access logs for Amazon S3\. CloudTrail logs provide you with detailed API tracking for Amazon S3 bucket\-level and object\-level operations\. Server access logs for Amazon S3 provide you visibility into object\-level operations on your data in Amazon S3\. For more information about server access logs, see [Logging requests using server access logging](ServerLogs.md)\.

You can also use CloudTrail logs together with CloudWatch for Amazon S3\. CloudTrail integration with CloudWatch Logs delivers S3 bucket\-level API activity captured by CloudTrail to a CloudWatch log stream in the CloudWatch log group that you specify\. You can create CloudWatch alarms for monitoring specific API activity and receive email notifications when the specific API activity occurs\. For more information about CloudWatch alarms for monitoring specific API activity, see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\. For more information about using CloudWatch with Amazon S3, see [Monitoring metrics with Amazon CloudWatch](cloudwatch-monitoring.md)\.

## CloudTrail tracking with Amazon S3 SOAP API calls<a name="cloudtrail-s3-soap"></a>

CloudTrail tracks Amazon S3 SOAP API calls\. Amazon S3 SOAP support over HTTP is deprecated, but it is still available over HTTPS\. For more information about Amazon S3 SOAP support, see [Appendix a: Using the SOAP API](SOAPAPI3.md)\. 

**Important**  
Newer Amazon S3 features are not supported for SOAP\. We recommend that you use either the REST API or the AWS SDKs\.


**Amazon S3 SOAP actions tracked by CloudTrail logging**  

| SOAP API name | API event name used in CloudTrail log | 
| --- | --- | 
|  [ListAllMyBuckets](https://docs.aws.amazon.com/AmazonS3/latest/API/SOAPListAllMyBuckets.html)  | ListBuckets | 
|  [CreateBucket](https://docs.aws.amazon.com/AmazonS3/latest/API/SOAPCreateBucket.html)  | CreateBucket | 
|  [DeleteBucket](https://docs.aws.amazon.com/AmazonS3/latest/API/SOAPDeleteBucket.html)  | DeleteBucket | 
|  [GetBucketAccessControlPolicy](https://docs.aws.amazon.com/AmazonS3/latest/API/SOAPGetBucketAccessControlPolicy.html)  | GetBucketAcl | 
|  [SetBucketAccessControlPolicy](https://docs.aws.amazon.com/AmazonS3/latest/API/SOAPSetBucketAccessControlPolicy.html)  | PutBucketAcl | 
|  [GetBucketLoggingStatus](https://docs.aws.amazon.com/AmazonS3/latest/API/SOAPGetBucketLoggingStatus.html)  | GetBucketLogging | 
|  [SetBucketLoggingStatus](https://docs.aws.amazon.com/AmazonS3/latest/API/SOAPSetBucketLoggingStatus.html)  | PutBucketLogging | 

 For more information about CloudTrail and Amazon S3, see the following topics: 

**Topics**
+ [Using CloudTrail logs with Amazon S3 server access logs and CloudWatch Logs](#cloudtrail-logging-vs-server-logs)
+ [CloudTrail tracking with Amazon S3 SOAP API calls](#cloudtrail-s3-soap)
+ [Amazon S3 CloudTrail events](cloudtrail-logging-s3-info.md)
+ [CloudTrail log file entries for Amazon S3 and S3 on Outposts](cloudtrail-logging-understanding-s3-entries.md)
+ [Enabling CloudTrail event logging for S3 buckets and objects](enable-cloudtrail-logging-for-s3.md)
+ [Identifying Amazon S3 requests using CloudTrail](cloudtrail-request-identification.md)