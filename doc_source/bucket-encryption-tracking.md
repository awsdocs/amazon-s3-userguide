# Monitoring default encryption with AWS CloudTrail and Amazon EventBridge<a name="bucket-encryption-tracking"></a>

**Important**  
Amazon S3 now applies server\-side encryption with Amazon S3 managed keys \(SSE\-S3\) as the base level of encryption for every bucket in Amazon S3\. Starting January 5, 2023, all new object uploads to Amazon S3 are automatically encrypted at no additional cost and with no impact on performance\. The automatic encryption status for S3 bucket default encryption configuration and for new object uploads is available in AWS CloudTrail logs, S3 Inventory, S3 Storage Lens, the Amazon S3 console, and as an additional Amazon S3 API response header in the AWS Command Line Interface and AWS SDKs\. For more information, see [Default encryption FAQ](https://docs.aws.amazon.com/AmazonS3/latest/userguide/default-encryption-faq.html)\.

You can track default encryption configuration requests for Amazon S3 buckets by using AWS CloudTrail events\. The following API event names are used in CloudTrail logs:
+ `PutBucketEncryption`
+ `GetBucketEncryption`
+ `DeleteBucketEncryption`

You can also create EventBridge rules to match the CloudTrail events for these API calls\. For more information about CloudTrail events, see [Enable logging for objects in a bucket using the console](enable-cloudtrail-logging-for-s3.md#enable-cloudtrail-events)\. For more information about EventBridge events, see [Events from AWS services](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-service-event.html)\.

You can use CloudTrail logs for object\-level Amazon S3 actions to track `PUT` and `POST` requests to Amazon S3\. You can use these actions to verify whether default encryption is being used to encrypt objects when incoming `PUT` requests don't have encryption headers\.

When Amazon S3 encrypts an object by using the default encryption settings, the log includes one of the following fields as the name\-value pair: `"SSEApplied":"Default_SSE_S3"` or `"SSEApplied":"Default_SSE_KMS"`\.

When Amazon S3 encrypts an object by using the `PUT` encryption headers, the log includes one of the following fields as the name\-value pair: `"SSEApplied":"SSE_S3"`, `"SSEApplied":"SSE_KMS"` or `"SSEApplied":"SSE_C"`\. 

For multipart uploads, this information is included in your `InitiateMultipartUpload` API operation requests\. For more information about using CloudTrail and CloudWatch, see [Monitoring Amazon S3](monitoring-overview.md)\.