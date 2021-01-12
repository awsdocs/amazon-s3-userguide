--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Monitoring default encryption with CloudTrail and CloudWatch<a name="bucket-encryption-tracking"></a>

You can track default encryption configuration requests for Amazon S3 buckets using AWS CloudTrail events\. The following API event names are used in CloudTrail logs:
+ `PutBucketEncryption`
+ `GetBucketEncryption`
+ `DeleteBucketEncryption`

You can also create Amazon CloudWatch Events with S3 bucket\-level operations as the event type\. For more information about CloudTrail events, see [Using the Amazon S3 console](enable-cloudtrail-logging-for-s3.md#enable-cloudtrail-events)\.

You can use CloudTrail logs for object\-level Amazon S3 actions to track `PUT` and `POST` requests to Amazon S3\. You can use these actions to verify whether default encryption is being used to encrypt objects when incoming `PUT` requests don't have encryption headers\.

When Amazon S3 encrypts an object using the default encryption settings, the log includes the following field as the name/value pair: `"SSEApplied":"Default_SSE_S3" or "SSEApplied":"Default_SSE_KMS"`\.

When Amazon S3 encrypts an object using the `PUT` encryption headers, the log includes one of the following fields as the name/value pair: `"SSEApplied":"SSE_S3", "SSEApplied":"SSE_KMS` or `"SSEApplied":"SSE_C"`\. 

For multipart uploads, this information is included in the `InitiateMultipartUpload` API requests\. For more information about using CloudTrail and CloudWatch, see [Monitoring Amazon S3](monitoring-overview.md)\.