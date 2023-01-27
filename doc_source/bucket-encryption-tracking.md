# Monitoring default encryption with CloudTrail and CloudWatch<a name="bucket-encryption-tracking"></a>

**Important**  
Amazon S3 now applies server\-side encryption with Amazon S3 managed keys \(SSE\-S3\) as the base level of encryption for every bucket in Amazon S3\. Starting January 5, 2023, all new object uploads to Amazon S3 will be automatically encrypted at no additional cost and with no impact on performance\. Currently, the automatic encryption status for S3 bucket default encryption configuration and for new object uploads is available in AWS CloudTrail logs, S3 Inventory, and S3 Storage Lens\. During the next few weeks, the automatic encryption status will also be rolled out to the Amazon S3 console and as an additional Amazon S3 API response header in the AWS Command Line Interface and AWS SDKs\. When this update is complete in all AWS Regions, we will update the documentation\. For more information, see [Default encryption FAQ](https://docs.aws.amazon.com/AmazonS3/latest/userguide/default-encryption-faq.html)\.

You can track default encryption configuration requests for Amazon S3 buckets using AWS CloudTrail events\. The following API event names are used in CloudTrail logs:
+ `PutBucketEncryption`
+ `GetBucketEncryption`
+ `DeleteBucketEncryption`

You can also create Amazon CloudWatch Events with S3 bucket\-level operations as the event type\. For more information about CloudTrail events, see [Enable logging for objects in a bucket using the console](enable-cloudtrail-logging-for-s3.md#enable-cloudtrail-events)\.

You can use CloudTrail logs for object\-level Amazon S3 actions to track `PUT` and `POST` requests to Amazon S3\. You can use these actions to verify whether default encryption is being used to encrypt objects when incoming `PUT` requests don't have encryption headers\.

When Amazon S3 encrypts an object using the default encryption settings, the log includes the following field as the name/value pair: `"SSEApplied":"Default_SSE_S3"` or `"SSEApplied":"Default_SSE_KMS"`\.

When Amazon S3 encrypts an object using the `PUT` encryption headers, the log includes one of the following fields as the name/value pair: `"SSEApplied":"SSE_S3"`, `"SSEApplied":"SSE_KMS`, or `"SSEApplied":"SSE_C"`\. 

For multipart uploads, this information is included in the `InitiateMultipartUpload` API requests\. For more information about using CloudTrail and CloudWatch, see [Monitoring Amazon S3](monitoring-overview.md)\.