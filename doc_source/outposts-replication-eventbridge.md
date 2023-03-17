# Using EventBridge for S3 Replication on Outposts<a name="outposts-replication-eventbridge"></a>

Amazon S3 on Outposts is integrated with Amazon EventBridge and uses the `s3-outposts` namespace\. EventBridge is a serverless event bus service that you can use to connect your applications with data from a variety of sources\. For more information, see [What is Amazon EventBridge?](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html) in the *Amazon EventBridge User Guide*\. 

To assist in troubleshooting any replication configuration issues, you can set up Amazon EventBridge to receive notifications about replication failure events\. EventBridge can notify you in instances when objects don't replicate to their destination Outposts\. For more information about the current state of an object that's being replicated, see [Replication status overview](manage-outposts-replication.md#outposts-replication-status-overview)\.

Whenever certain events happen in your Outposts bucket, S3 on Outposts can send events to EventBridge\. Unlike other destinations, you don't need to select which event types that you want to deliver\. You can also use EventBridge rules to route events to additional targets\. After EventBridge is enabled, S3 on Outposts sends all of the following events to EventBridge\. 


| Event type | Description  | Namespace | 
| --- | --- | --- | 
|  `OperationFailedReplication`  |  The replication of an object within a replication rule failed\. For more information about S3 Replication on Outposts failure reasons, see [Using EventBridge to view S3 Replication on Outposts failure reasons](#outposts-replication-failure-codes)\.  |  `s3-outposts`  | 

## Using EventBridge to view S3 Replication on Outposts failure reasons<a name="outposts-replication-failure-codes"></a>

The following table lists S3 Replication on Outposts failure reasons\. You can configure an EventBridge rule to publish and view the failure reason through Amazon Simple Queue Service \(Amazon SQS\), Amazon Simple Notification Service \(Amazon SNS\), AWS Lambda, or Amazon CloudWatch Logs\. For more information about the permissions that are required to use these resources for EventBridge, see [Using resource\-based policies for EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-use-resource-based.html)\.


| Replication failure reason | Description | 
| --- | --- | 
| AssumeRoleNotPermitted | S3 on Outposts can't assume the AWS Identity and Access Management \(IAM\) role that's specified in the replication configuration\. | 
| DstBucketNotFound | S3 on Outposts can't find the destination bucket that's specified in the replication configuration\. | 
| DstBucketUnversioned | Versioning isn't enabled on the Outposts destination bucket\. To replicate objects with S3 Replication on Outposts, you must enable versioning on the destination bucket\. | 
| DstDelObjNotPermitted | S3 on Outposts can't replicate deletes to the destination bucket\. The s3\-outposts:ReplicateDelete permission might be missing for the destination bucket\. | 
| DstMultipartCompleteNotPermitted | S3 on Outposts can't complete a multipart upload of objects in the destination bucket\. The s3\-outposts:ReplicateObject permission might be missing for the destination bucket\.  | 
| DstMultipartInitNotPermitted | S3 on Outposts can't initiate a multipart upload of objects to the destination bucket\. The s3\-outposts:ReplicateObject permission might be missing for the destination bucket\.  | 
| DstMultipartPartUploadNotPermitted | S3 on Outposts can't upload multipart objects in the destination bucket\. The s3\-outposts:ReplicateObject permission might be missing for the destination bucket\.  | 
| DstOutOfCapacity | S3 on Outposts can't replicate to the destination Outpost because the Outpost is out of S3 storage capacity\. | 
| DstPutObjNotPermitted | S3 on Outposts can't replicate objects to the destination bucket\. The s3\-outposts:ReplicateObject permission might be missing for the destination bucket\.  | 
| DstPutTaggingNotPermitted | S3 on Outposts can't replicate object tags to the destination bucket\. The s3\-outposts:ReplicateObject permission might be missing for the destination bucket\.  | 
| DstVersionNotFound | S3 on Outposts can't find the required object version in the destination bucket in order to replicate that object version's metadata\. | 
| SrcBucketReplicationConfigMissing | S3 on Outposts can't find a replication configuration for the access point that's associated with the source Outposts bucket\.  | 
| SrcGetObjNotPermitted | S3 on Outposts can't access the object in the source bucket for replication\. The s3\-outposts:GetObjectVersionForReplication permission might be missing for the source bucket\.  | 
| SrcGetTaggingNotPermitted | S3 on Outposts can't access the object tag information from the source bucket\. The s3\-outposts:GetObjectVersionTagging permission might be missing for the source bucket\. | 
| SrcHeadObjectNotPermitted | S3 on Outposts can't retrieve object metadata from the source bucket\. The s3\-outposts:GetObjectVersionForReplication permission might be missing for the source bucket\.  | 
| SrcObjectNotEligible | The object isn't eligible for replication\. The object or its object tags don't match the replication configuration\. | 

For more information about troubleshooting replication, see the following topics:
+ [Creating an IAM role](outposts-replication-prerequisites-config.md#outposts-rep-pretwo)
+ [Troubleshooting replication](manage-outposts-replication.md#outposts-replication-troubleshoot)

## Monitoring EventBridge with CloudWatch<a name="outposts-replication-eventbridge-cw"></a>

For monitoring, Amazon EventBridge integrates with Amazon CloudWatch\. EventBridge automatically sends metrics to CloudWatch every minute\. These metrics include the number of [events](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-events.html) that have been matched by a [rule](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-rules.html) and the number of times a [target](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-targets.html) is invoked by a rule\. When a rule runs in EventBridge, all of the targets associated with the rule are invoked\. You can monitor your EventBridge behavior through CloudWatch in the following ways\.
+ You can monitor the available [EventBridge metrics](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-monitoring.html#eb-metrics) for your EventBridge rules from the CloudWatch dashboard\. Then, you can use CloudWatch features, such as CloudWatch alarms, to set alarms on certain metrics\. If those metrics reach the custom threshold values that you've specified in the alarms, you receive notifications and can take action accordingly\. 
+ You can set Amazon CloudWatch Logs as a target of your EventBridge rule\. Then, EventBridge creates log streams and CloudWatch Logs stores the text from the events as log entries\. For more information, see [EventBridge and CloudWatch Logs](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-use-resource-based.html#eb-cloudwatchlogs-permissions)\.

For more information about debugging EventBridge event delivery and archiving events, see the following topics:
+ [Event retry policy and using dead\-letter queues](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-rule-dlq.html)
+ [Archiving EventBridge events](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-archive-event.html)