--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Amazon S3 CloudTrail events<a name="cloudtrail-logging-s3-info"></a>

CloudTrail is enabled on your AWS account when you create the account\. When supported event activity occurs in Amazon S3, that activity is recorded in a CloudTrail event along with other AWS service events in **Event history**\. You can view, search, and download recent events in your AWS account\. For more information, see [Viewing Events with CloudTrail Event History](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events.html)\. 

For an ongoing record of events in your AWS account, including events for Amazon S3, create a trail\. A trail enables CloudTrail to deliver log files to an Amazon S3 bucket\. By default, when you create a trail in the console, the trail applies to all Regions\. The trail logs events from all Regions in the AWS partition and delivers the log files to the Amazon S3 bucket that you specify\. Additionally, you can configure other AWS services to further analyze and act upon the event data collected in CloudTrail logs\. For more information, see the following: 
+ [Creating a trail for your AWS account](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail.html)
+ [AWS Service Integrations with CloudTrail Logs](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-aws-service-specific-topics.html#cloudtrail-aws-service-specific-topics-integrations)
+ [Configuring Amazon SNS Notifications for CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html)
+ [Receiving CloudTrail Log Files from Multiple Regions](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/receive-cloudtrail-log-files-from-multiple-regions.html)
+ [Receiving CloudTrail Log Files from Multiple Accounts](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)

Every event or log entry contains information about who generated the request\. The identity information helps you determine the following: 
+ Whether the request was made with root or IAM user credentials\.
+ Whether the request was made with temporary security credentials for a role or federated user\.
+ Whether the request was made by another AWS service\.

For more information, see the [CloudTrail userIdentity Element](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-user-identity.html)\.

You can store your log files in your bucket for as long as you want, but you can also define Amazon S3 Lifecycle rules to archive or delete log files automatically\. By default, your log files are encrypted by using Amazon S3 server\-side encryption \(SSE\)\.

## How CloudTrail captures requests made to Amazon S3<a name="cloudtrail-logging-s3-requests"></a>

By default, CloudTrail logs S3 bucket\-level API calls that were made in the last 90 days, but not log requests made to objects\. Bucket\-level calls include events like `CreateBucket`, `DeleteBucket`, `PutBucketLifeCycle`, `PutBucketPolicy`, etc\. You can see bucket\-level events on the CloudTrail console\. However, you can't view data events \(Amazon S3 object\-level calls\) there—you must parse or query CloudTrail logs for them\. 

For information about what Amazon S3 API calls are captured by CloudTrail, see [Amazon S3 CloudTrail events](#cloudtrail-logging-s3-info)\. 

## Amazon S3 account\-level actions tracked by CloudTrail logging<a name="cloudtrail-account-level-tracking"></a>

CloudTrail logs account\-level actions\. Amazon S3 records are written together with other AWS service records in a log file\. CloudTrail determines when to create and write to a new file based on a time period and file size\. 

The tables in this section list the Amazon S3 account\-level actions that are supported for logging by CloudTrail\.

Amazon S3 account\-level API actions tracked by CloudTrail logging appear as the following event names:
+ [ DeletePublicAccessBlock](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeletePublicAccessBlock.html)
+ [ GetPublicAccessBlock](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetPublicAccessBlock.html)
+ [ PutPublicAccessBlock](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutPublicAccessBlock.html)

## Amazon S3 bucket\-level actions tracked by CloudTrail logging<a name="cloudtrail-bucket-level-tracking"></a>

By default, CloudTrail logs bucket\-level actions\. Amazon S3 records are written together with other AWS service records in a log file\. CloudTrail determines when to create and write to a new file based on a time period and file size\. 

The tables in this section list the Amazon S3 bucket\-level actions that are supported for logging by CloudTrail\.

Amazon S3 bucket\-level API actions tracked by CloudTrail logging appear as the following event names:
+ [CreateBucket](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUT.html) 
+ [DeleteBucket](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketDELETE.html)
+ [DeleteBucketCors](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketDELETEcors.html)
+ [DeleteBucketEncryption](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketDELETEencryption.html)
+ [DeleteBucketLifecycle](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketDELETElifecycle.html)
+ [DeleteBucketPolicy](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketDELETEpolicy.html)
+ [DeleteBucketReplication ](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketDELETEreplication.html)
+ [DeleteBucketTagging](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketDELETEtagging.html)
+ [ DeletePublicAccessBlock](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeletePublicAccessBlock.html)
+ [GetBucketCors](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETcors.html)
+ [GetBucketEncryption](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETencryption.html)
+ [GetBucketLifecycle](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETencryption.html) 
+ [GetBucketLocation](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETlocation.html) 
+ [GetBucketLogging](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETlogging.html) 
+ [GetBucketNotification](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETnotification.html)
+ [GetBucketPolicy](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETpolicy.html)
+ [GetBucketReplication](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETreplication.html)
+ [GetBucketRequestPayment](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTrequestPaymentGET.html)
+ [GetBucketTagging](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETtagging.html)
+ [GetBucketVersioning](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETversioningStatus.html) 
+ [GetBucketWebsite](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETwebsite.html) 
+ [ GetPublicAccessBlock](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetPublicAccessBlock.html) 
+ [ListBuckets](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTServiceGET.html) 
+ [PutBucketAcl](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTacl.html) 
+ [PutBucketCors](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTcors.html) 
+ [PutBucketEncryption](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTencryption.html)
+ [PutBucketLifecycle](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTlifecycle.html) 
+ [PutBucketLogging](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTlogging.html)
+ [PutBucketNotification ](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTnotification.html)
+ [PutBucketPolicy](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTpolicy.html) 
+ [PutBucketReplication](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTreplication.html) 
+ [PutBucketRequestPayment](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTrequestPaymentPUT.html)
+ [PutBucketTagging ](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTtagging.html) 
+ [PutBucketVersioning](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTVersioningStatus.html)
+ [PutBucketWebsite](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTwebsite.html) 
+ [ PutPublicAccessBlock](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutPublicAccessBlock.html) 



In addition to these API operations, you can also use the [OPTIONS object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTOPTIONSobject.html) object\-level action\. This action is treated like a bucket\-level action in CloudTrail logging because the action checks the cors configuration of a bucket\.

## Amazon S3 object\-level actions tracked by AWS CloudTrail logging<a name="cloudtrail-object-level-tracking"></a>

You can also get CloudTrail logs for object\-level Amazon S3 actions\. To do this, enable data events for your S3 bucket or all buckets in your account\. When an object\-level action occurs in your account, CloudTrail evaluates your trail settings\. If the event matches the object that you specified in a trail, the event is logged\. For more information, see [Enabling CloudTrail event logging for S3 buckets and objects](enable-cloudtrail-logging-for-s3.md) and [Logging Data Events for Trails](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-data-events-with-cloudtrail.html) in the *AWS CloudTrail User Guide*\. 

The following object\-level API actions are logged as CloudTrail events:
+ [AbortMultipartUpload](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadAbort.html)
+ [CompleteMultipartUpload](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadComplete.html)
+ [DeleteObjects](https://docs.aws.amazon.com/AmazonS3/latest/API/multiobjectdeleteapi.html)
+ [DeleteObject](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectDELETE.html)
+ [GetObject](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGET.html)
+ [GetObjectAcl](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGETacl.html)
+ [GetObjectTagging](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGETtagging.html)
+ [GetObjectTorrent](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGETtorrent.html)
+ [HeadObject](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectHEAD.html)
+ [CreateMultipartUpload](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadInitiate.html)
+ [ListParts](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadListParts.html)
+ [PostObject](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOST.html)
+ [RestoreObject](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOSTrestore.html)
+ [PutObject](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPUT.html)
+ [PutObjectAcl](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPUTacl.html)
+ [PutObjectTagging](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPUTtagging.html)
+ [CopyObject](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectCOPY.html)
+ [UploadPart](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadUploadPart.html)
+ [UploadPartCopy](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadUploadPartCopy.html)

In addition to these operations, you can use the following bucket\-level operations to get CloudTrail logs as object\-level Amazon S3 actions under certain conditions:
+ [GET Bucket \(List Objects\) Version 2](https://docs.aws.amazon.com/AmazonS3/latest/API/v2-RESTBucketGET.html) – Select a prefix specified in the trail\.
+ [GET Bucket Object versions](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETVersion.html) – Select a prefix specified in the trail\.
+ [HEAD Bucket](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketHEAD.html) – Specify a bucket and an empty prefix\.
+ [Delete Multiple Objects](https://docs.aws.amazon.com/AmazonS3/latest/API/multiobjectdeleteapi.html) – Specify a bucket and an empty prefix\. 
**Note**  
CloudTrail does not log key names for the keys that are deleted using the Delete Multiple Objects operation\.

### Object\-level actions in cross\-account scenarios<a name="cloudtrail-object-level-crossaccount"></a>

The following are special use cases involving the object\-level API calls in cross\-account scenarios and how CloudTrail logs are reported\. CloudTrail always delivers logs to the requester \(who made the API call\)\. When setting up cross\-account access, consider the examples in this section\.

**Note**  
The examples assume that CloudTrail logs are appropriately configured\. 

#### Example 1: CloudTrail delivers access logs to the bucket owner<a name="cloudtrail-crossaccount-example1"></a>

CloudTrail delivers access logs to the bucket owner only if the bucket owner has permissions for the same object API\. Consider the following cross\-account scenario:
+ Account\-A owns the bucket\.
+ Account\-B \(the requester\) tries to access an object in that bucket\.

CloudTrail always delivers object\-level API access logs to the requester\. In addition, CloudTrail also delivers the same logs to the bucket owner only if the bucket owner has permissions for the same API actions on that object\. 

**Note**  
If the bucket owner is also the object owner, the bucket owner gets the object access logs\. Otherwise, the bucket owner must get permissions, through the object ACL, for the same object API to get the same object\-access API logs\.

#### Example 2: CloudTrail does not proliferate email addresses used in setting object ACLs<a name="cloudtrail-crossaccount-example2"></a>

Consider the following cross\-account scenario:
+ Account\-A owns the bucket\.
+  Account\-B \(the requester\) sends a request to set an object ACL grant using an email address\. For information about ACLs, see [Managing access with ACLs](acl-overview.md)\.

The request gets the logs along with the email information\. However, the bucket owner—if they are eligible to receive logs, as in example 1—gets the CloudTrail log reporting the event\. However, the bucket owner doesn't get the ACL configuration information, specifically the grantee email and the grant\. The only information that the log tells the bucket owner is that an ACL API call was made by Account\-B\.