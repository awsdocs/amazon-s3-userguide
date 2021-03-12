--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# S3 Object Lock legal hold<a name="batch-ops-legal-hold"></a>

S3 Object Lock enables you to place a legal hold on an object version\. Like setting a retention period, a legal hold prevents an object version from being overwritten or deleted\. However, a legal hold doesn't have an associated retention period and remains in effect until removed\. 

You can use S3 Batch Operations with Object Lock to add legal holds to *many* Amazon S3 objects at once\. You can do this by listing the target objects in your manifest and submitting that list to Batch Operations\. Your S3 Batch Operations job with Object Lock legal hold runs until completion, until cancellation, or until a failure state is reached\.

S3 Batch Operations verifies that Object Lock is enabled on your S3 bucket before processing any keys in the manifest\. To perform the object operations and bucket level validation, S3 Batch Operations needs `s3:PutObjectLegalHold` and `s3:GetBucketObjectLockConfiguration` in an IAM role allowing S3 Batch Operations to call S3 Object Lock on your behalf\. 

When you create the S3 Batch Operations job to remove the legal hold, you just need to specify *Off* as the legal hold status\. For more information, see [Managing Object Lock ](object-lock-managing.md)\.

For information about how to use this operation with the REST API, see `S3PutObjectLegalHold` in the [CreateJob](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_CreateJob.html) operation in the *Amazon Simple Storage Service API Reference*\. 

For an example use of this operation, see [Using the AWS SDK Java](batch-ops-legal-hold-off.md#batch-ops-examples-java-object-lock-legalhold)\. 

## Restrictions and limitations<a name="batch-ops-legal-hold-restrictions"></a>
+ S3 Batch Operations does not make any bucket level changes\.
+ All objects listed in the manifest must be in the same bucket\.
+ Versioning and S3 Object Lock must be configured on the bucket where the job is performed\.
+ The operation works on the latest version of the object unless a version is explicitly specified in the manifest\.
+ `s3:PutObjectLegalHold` permission is required in your IAM role to add or remove legal hold from objects\.
+ `s3:GetBucketObjectLockConfiguration` IAM permission is required to confirm that S3 Object Lock is enabled for the S3 bucket\. 