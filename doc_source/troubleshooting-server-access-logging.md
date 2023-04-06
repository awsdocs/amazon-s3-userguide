# Troubleshoot server access logging<a name="troubleshooting-server-access-logging"></a>

The following topics can help you troubleshoot issues that you might encounter when setting up logging with Amazon S3\.

**Topics**
+ [Common error messages when setting up logging](#common-errors)
+ [Troubleshooting delivery failures](#delivery-failures)

## Common error messages when setting up logging<a name="common-errors"></a>

The following common error messages can appear when you're enabling logging through the AWS Command Line Interface \(AWS CLI\) and AWS SDKs: 

Error: Cross S3 location logging not allowed

If the target bucket is in a different Region than the source bucket, a Cross S3 location logging not allowed error occurs\. To resolve this error, make sure that the target bucket configured to receive the access logs is in the same AWS Region and AWS account as the source bucket\.

Error: The owner for the bucket to be logged and the target bucket must be the same

When you're enabling server access logging, this error occurs if the specified target bucket belongs to a different account\. To resolve this error, make sure that the target bucket is in the same AWS account as the source bucket\.

**Note**  
We recommend that you choose a target bucket that's different from the source bucket\. When the source bucket and target bucket are the same, additional logs are created for the logs that are written to the bucket, which can increase your storage bill\. These extra logs about logs can also make it difficult to find the particular logs that you're looking for\. For simpler log management, we recommend saving access logs in a different bucket\. For more information, see [How do I enable log delivery?](ServerLogs.md#server-access-logging-overview)\.

Error: The target bucket for logging does not exist

The target bucket must exist prior to setting the configuration\. This error indicates that the target bucket doesn't exist or can't be found\. Make sure that the bucket name is spelled correctly, and then try again\.

Error: Target grants not allowed for bucket owner enforced buckets

This error indicates that the target bucket uses the bucket owner enforced setting for S3 Object Ownership\. The bucket owner enforced setting doesn't support target grants\. For more information, see [Permissions for log delivery](enable-server-access-logging.md#grant-log-delivery-permissions-general)\.

## Troubleshooting delivery failures<a name="delivery-failures"></a>

To avoid server access logging issues, make sure that you're following these best practices:
+ **The S3 log delivery group has write access to the target bucket** – The S3 log delivery group delivers server access logs to the target bucket\. A bucket policy or bucket access control list \(ACL\) can be used to grant write access to the target bucket\. However, we recommend that you use a bucket policy instead of an ACL\. For more information about how to grant write access to your target bucket, see [Permissions for log delivery](enable-server-access-logging.md#grant-log-delivery-permissions-general)\.
**Note**  
If the target bucket uses the bucket owner enforced setting for Object Ownership, be aware of the following:   
ACLs are disabled and no longer affect permissions\. This means that you can't update your bucket ACL to grant access to the S3 log delivery group\. Instead, to grant access to the logging service principal, you must update the bucket policy for the target bucket\. 
You can't include target grants in your `PutBucketLogging` configuration\. 
+ **The bucket policy for the target bucket allows access to the logs** – Check the bucket policy of the target bucket\. Search the bucket policy for any statements that contain `"Effect": "Deny"`\. Then, verify that the `Deny` statement isn't preventing access logs from being written to the bucket\.
+ **S3 Object Lock isn't enabled on the target bucket** – Check if the target bucket has Object Lock enabled\. Object Lock blocks server access log delivery\. You must choose a target bucket that doesn't have Object Lock enabled\.
+ **Amazon S3 managed keys \(SSE\-S3\) is selected if default encryption is enabled on the target bucket** – You can use default bucket encryption on the target bucket only if you use server\-side encryption with Amazon S3 managed keys \(SSE\-S3\)\. Default server\-side encryption with AWS Key Management Service \(AWS KMS\) keys \(SSE\-KMS\) is not supported for server access logging target buckets\. For more information about how to enable default encryption, see [Configuring default encryption](default-bucket-encryption.md)\.
+ **The target bucket does not have Requester Pays enabled** – Using a Requester Pays bucket as the target bucket for server access logging is not supported\. To allow delivery of server access logs, disable the Requester Pays option on the target bucket\.
+ **Review your AWS Organizations service control policy** – When you're using AWS Organizations, check the service control policies to make sure that Amazon S3 access is allowed\. Service control policies specify the maximum permissions for the affected accounts\. Search the service control policy for any statements that contain `"Effect": "Deny"` and verify that `Deny` statements aren't preventing any access logs from being written to the bucket\. For more information, see [Service control policies \(SCPs\)](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html) in the *AWS Organizations User Guide*\.
+ **Allow some time for recent logging configuration changes to take effect** – Enabling server access logging for the first time, or changing the target bucket for logs, requires time to fully take effect\. It might take longer than an hour for all requests to be properly logged and delivered\. 

  To check for log delivery failures, enable request metrics in Amazon CloudWatch\. If the logs are not delivered within a few hours, look for the `4xxErrors` metric, which can indicate log delivery failures\. For more information about enabling request metrics, see [Creating a CloudWatch metrics configuration for all the objects in your bucket](configure-request-metrics-bucket.md)\.