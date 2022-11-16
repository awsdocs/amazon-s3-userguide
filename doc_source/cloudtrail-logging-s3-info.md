# Amazon S3 CloudTrail events<a name="cloudtrail-logging-s3-info"></a>

CloudTrail is enabled on your AWS account when you create the account\. When supported event activity occurs in Amazon S3, that activity is recorded in a CloudTrail event along with other AWS service events in **Event history**\. You can view, search, and download recent events in your AWS account\. For more information, see [Viewing Events with CloudTrail Event History](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events.html)\. 

For an ongoing record of events in your AWS account, including events for Amazon S3, create a trail\. A trail enables CloudTrail to deliver log files to an Amazon S3 bucket\. By default, when you create a trail in the console, the trail applies to all Regions\. The trail logs events from all Regions in the AWS partition and delivers the log files to the Amazon S3 bucket that you specify\. Additionally, you can configure other AWS services to further analyze and act upon the event data collected in CloudTrail logs\. For more information, see the following: 
+ [Creating a trail for your AWS account](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail.html)
+ [AWS services integrations with CloudTrail logs](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-aws-service-specific-topics.html#cloudtrail-aws-service-specific-topics-integrations)
+ [Configuring Amazon SNS notifications for CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html)
+ [Receiving CloudTrail log files from multiple Regions](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/receive-cloudtrail-log-files-from-multiple-regions.html)
+ [Receiving CloudTrail log files from multiple accounts](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)

Every event or log entry contains information about who generated the request\. The identity information helps you determine the following: 
+ Whether the request was made with root or IAM user credentials
+ Whether the request was made with temporary security credentials for a role or federated user
+ Whether the request was made by another AWS service

For more information, see the [CloudTrail userIdentity Element](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-user-identity.html)\.

You can store your log files in your bucket for as long as you want, but you can also define Amazon S3 Lifecycle rules to archive or delete log files automatically\. By default, your log files are encrypted by using Amazon S3 server\-side encryption \(SSE\)\.

## How CloudTrail captures requests made to Amazon S3<a name="cloudtrail-logging-s3-requests"></a>

By default, CloudTrail logs S3 bucket\-level API calls that were made in the last 90 days, but not log requests made to objects\. Bucket\-level calls include events such as `CreateBucket`, `DeleteBucket`, `PutBucketLifeCycle`, `PutBucketPolicy`, and so on\. You can see bucket\-level events on the CloudTrail console\. However, you can't view data events \(Amazon S3 object\-level calls\) there—you must parse or query CloudTrail logs for them\. 

## Amazon S3 account\-level actions tracked by CloudTrail logging<a name="cloudtrail-account-level-tracking"></a>

CloudTrail logs account\-level actions\. Amazon S3 records are written together with other AWS service records in a log file\. CloudTrail determines when to create and write to a new file based on a time period and file size\. 

The tables in this section list the Amazon S3 account\-level actions that are supported for logging by CloudTrail\.

Amazon S3 account\-level API actions tracked by CloudTrail logging appear as the following event names:
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeletePublicAccessBlock.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeletePublicAccessBlock.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetPublicAccessBlock.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetPublicAccessBlock.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutPublicAccessBlock.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutPublicAccessBlock.html)

## Amazon S3 bucket\-level actions that are tracked by CloudTrail logging<a name="cloudtrail-bucket-level-tracking"></a>

By default, CloudTrail logs bucket\-level actions\. Amazon S3 records are written together with other AWS service records in a log file\. CloudTrail determines when to create and write to a new file based on a time period and file size\. 

The tables in this section list the Amazon S3 bucket\-level actions that are supported for logging by CloudTrail\.

Amazon S3 bucket\-level API actions tracked by CloudTrail logging appear as the following event names:
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_CreateBucket.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CreateBucket.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteBucket.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteBucket.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteBucketCors.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteBucketCors.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteBucketEncryption.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteBucketEncryption.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteBucketLifecycle.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteBucketLifecycle.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteBucketPolicy.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteBucketPolicy.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteBucketReplication.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteBucketReplication.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteBucketTagging.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteBucketTagging.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeletePublicAccessBlock.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeletePublicAccessBlock.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketAcl.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketAcl.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketCors.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketCors.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketEncryption.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketEncryption.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketLifecycleConfiguration.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketLifecycleConfiguration.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketLocation.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketLocation.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketLogging.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketLogging.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketNotificationConfiguration.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketNotificationConfiguration.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectLockConfiguration.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectLockConfiguration.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketPolicy.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketPolicy.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketReplication.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketReplication.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketRequestPayment.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketRequestPayment.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketTagging.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketTagging.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketVersioning.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketVersioning.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketWebsite.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketWebsite.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetPublicAccessBlock.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetPublicAccessBlock.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListBuckets.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListBuckets.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketAcl.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketAcl.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketCors.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketCors.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketEncryption.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketEncryption.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketLifecycleConfiguration.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketLifecycleConfiguration.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketLogging.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketLogging.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketNotificationConfiguration.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketNotificationConfiguration.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketPolicy.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketPolicy.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketReplication.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketReplication.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketRequestPayment.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketRequestPayment.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketTagging.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketTagging.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketVersioning.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketVersioning.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketWebsite.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketWebsite.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutPublicAccessBlock.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutPublicAccessBlock.html) 



In addition to these API operations, you can also use the [OPTIONS object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTOPTIONSobject.html) object\-level action\. This action is treated like a bucket\-level action in CloudTrail logging because the action checks the CORS configuration of a bucket\.

## Amazon S3 object\-level actions that are tracked by AWS CloudTrail logging<a name="cloudtrail-object-level-tracking"></a>

You can also get CloudTrail logs for object\-level Amazon S3 actions\. To do this, enable data events for your S3 bucket or all buckets in your account\. When an object\-level action occurs in your account, CloudTrail evaluates your trail settings\. If the event matches the object that you specified in a trail, the event is logged\. For more information, see [Enabling CloudTrail event logging for S3 buckets and objects](enable-cloudtrail-logging-for-s3.md) and [Logging Data Events for Trails](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-data-events-with-cloudtrail.html) in the *AWS CloudTrail User Guide*\.

The following object\-level API actions are logged as CloudTrail events:
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_AbortMultipartUpload.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_AbortMultipartUpload.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_CompleteMultipartUpload.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CompleteMultipartUpload.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteObjects.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteObjects.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteObject.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteObject.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectAcl.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectAcl.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectAttributes.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectAttributes.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectTagging.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectTagging.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadObject.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadObject.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_CreateMultipartUpload.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CreateMultipartUpload.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListParts.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListParts.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOST.html](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOST.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_RestoreObject.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_RestoreObject.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectAcl.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectAcl.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectTagging.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectTagging.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_UploadPart.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_UploadPart.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_UploadPartCopy.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_UploadPartCopy.html) 

In addition to these operations, you can use the following bucket\-level operations to get CloudTrail logs as object\-level Amazon S3 actions under certain conditions:
+ [GET Bucket Object \(List Objects\)](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListObjectsV2.html) Version 2 – Select a prefix specified in the trail\.
+ [GET Bucket Object Versions \(List Object Versions\)](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListObjectVersions.html) – Select a prefix specified in the trail\.
+ [HEAD Bucket](https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadBucket.html) – Specify a bucket and an empty prefix\.
+ [Delete Multiple Objects](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteObjects.html) – Specify a bucket and an empty prefix\. 
**Note**  
CloudTrail does not log key names for the keys that are deleted using the Delete Multiple Objects operation\.

### Object\-level actions in cross\-account scenarios<a name="cloudtrail-object-level-crossaccount"></a>

The following are special use cases involving the object\-level API calls in cross\-account scenarios and how CloudTrail logs are reported\. CloudTrail always delivers logs to the requester \(the account that made the API call\)\. When setting up cross\-account access, consider the examples in this section\.

**Note**  
The examples assume that CloudTrail logs are appropriately configured\. 

#### Example 1: CloudTrail delivers logs to the bucket owner<a name="cloudtrail-crossaccount-example1"></a>

CloudTrail delivers logs to the bucket owner even if the bucket owner does not have permissions for the same object API operation\. Consider the following cross\-account scenario:
+ Account A owns the bucket\.
+ Account B \(the requester\) tries to access an object in that bucket\.
+ Account C owns the object\. Account C might or might not be the same account as Account A\.

**Note**  
CloudTrail always delivers object\-level API logs to the requester \(Account B\)\. In addition, CloudTrail also delivers the same logs to the bucket owner \(Account A\) even when the bucket owner does not own the object \(Account C\) or have permissions for those same API operations on that object\.

#### Example 2: CloudTrail does not proliferate email addresses that are used in setting object ACLs<a name="cloudtrail-crossaccount-example2"></a>

Consider the following cross\-account scenario:
+ Account A owns the bucket\.
+  Account B \(the requester\) sends a request to set an object ACL grant by using an email address\. For more information about ACLs, see [Access control list \(ACL\) overview](acl-overview.md)\.

The requester gets the logs along with the email information\. However, the bucket owner—if they are eligible to receive logs, as in example 1—gets the CloudTrail log reporting the event\. However, the bucket owner doesn't get the ACL configuration information, specifically the grantee email address and the grant\. The only information that the log tells the bucket owner is that an ACL API call was made by Account B\.