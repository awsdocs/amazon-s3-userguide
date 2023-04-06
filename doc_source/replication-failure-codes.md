# Amazon S3 replication failure reasons<a name="replication-failure-codes"></a>

The following table lists Amazon S3 Replication failure reasons\. You can view these reasons by receiving the **failureReason** event with Amazon S3 Event Notifications\. You can receive S3 Event Notifications through Amazon Simple Queue Service \(Amazon SQS\), Amazon Simple Notification Service \(Amazon SNS\), or AWS Lambda\. For more information, see [Amazon S3 Event Notifications](NotificationHowTo.md)\.

You can also view these failure reasons in an S3 Batch Replication completion report\. For more information, see [Batch Replication completion report](s3-batch-replication-batch.md#batch-replication-completion-report)\.


| Replication failure reason | Description | 
| --- | --- | 
| AssumeRoleNotPermitted | Amazon S3 can't assume the AWS Identity and Access Management \(IAM\) role that's specified in the replication configuration or in the Batch Operations job\. | 
| DstBucketNotFound | Amazon S3 is unable to find the destination bucket that's specified in the replication configuration\. | 
| DstBucketObjectLockConfigMissing | To replicate objects from a source bucket with Object Lock enabled, the destination bucket must also have Object Lock enabled\. This error indicates that Object Lock might not be enabled in the destination bucket\. For more information, see [[Managing Object Lock](object-lock-managing.md)Managing Object Lock](object-lock-managing.md)\. | 
| DstBucketUnversioned | Versioning is not enabled for the S3 destination bucket\. To replicate objects with S3 Replication, enable versioning for the destination bucket\. | 
| DstDelObjNotPermitted | Amazon S3 is unable to replicate delete markers to the destination bucket\. The s3:ReplicateDelete permission might be missing for the destination bucket\. | 
| DstKmsKeyInvalidState | The AWS Key Management Service \(AWS KMS\) key for the destination bucket isn't in a valid state\. Review and enable the required AWS KMS key\. For more information about managing AWS KMS keys, see [Key states of AWS KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/key-state.html) in the AWS Key Management Service Developer Guide\. | 
| DstKmsKeyNotFound | The AWS KMS key that's configured for the destination bucket in the replication configuration doesn't exist\. | 
| DstMultipartCompleteNotPermitted | Amazon S3 is unable to complete multipart uploads of objects in the destination bucket\. The s3:ReplicateObject permission might be missing for the destination bucket\. | 
| DstMultipartInitNotPermitted | Amazon S3 is unable to initiate multipart uploads of objects to the destination bucket\. The s3:ReplicateObject permission might be missing for the destination bucket\.  | 
| DstMultipartPartUploadNotPermitted | Amazon S3 is unable to upload multipart objects to the destination bucket\. The s3:ReplicateObject permission might be missing for the destination bucket\.  | 
| DstObjectHardDeleted | S3 Batch Replication does not support re\-replicating objects deleted with the version ID of the object from the destination bucket\. This error is specific to Batch Replication\. | 
| DstPutAclNotPermitted | Amazon S3 is unable to replicate object access control lists \(ACLs\) to the destination bucket\. The s3:ReplicateObject permission might be missing for the destination bucket\. | 
| DstPutLegalHoldNotPermitted | Amazon S3 is unable to put an Object Lock legal hold on the destination objects when it is replicating immutable objects\. The s3:PutObjectLegalHold permission might be missing for the destination bucket\. For more information, see [Legal holds](object-lock-overview.md#object-lock-legal-holds)\. | 
|  DstPutObjectNotPermitted | Amazon S3 is unable to replicate objects to the destination bucket\. The s3:ReplicateObject or s3:ObjectOwnerOverrideToBucketOwner permissions might be missing for the destination bucket\. | 
| DstPutTaggingNotPermitted | Amazon S3 is unable to replicate object tags to the destination bucket\. The s3:ReplicateObject permission might be missing for the destination bucket\. | 
| DstVersionNotFound  | Amazon S3 is unable to find the required object version in the destination bucket for which metadata needs to be replicated\.  | 
| InitiateReplicationNotPermitted | Amazon S3 is unable to initiate replication on objects\. The s3:InitiateReplication permission might be missing for the Batch Operations job\. This error is specific to Batch Replication\. | 
| SrcBucketInvalidRegion | The source bucket is not in the same AWS Region as specified by the Batch Operations job\. This error is specific to Batch Replication\. | 
| SrcBucketNotFound | Amazon S3 is unable to find the source bucket\. | 
| SrcBucketReplicationConfigMissing | Amazon S3 couldn't find a replication configuration for the source bucket\. | 
| SrcGetAclNotPermitted | Amazon S3 is unable to access the object in the source bucket for replication\. The s3:GetObjectVersionAcl permission might be missing for the source bucket object\.  | 
| SrcGetObjectNotPermitted | Amazon S3 is unable to access the object in the source bucket for replication\. The s3:GetObjectVersionForReplication permission might be missing for the source bucket\.  | 
| SrcGetTaggingNotPermitted | Amazon S3 is unable to access object tag information from the source bucket\. The s3:GetObjectVersionTagging permission might be missing for the source bucket\. | 
| SrcHeadObjectNotPermitted | Amazon S3 is unable to retrieve object metadata from the source bucket\. The s3:GetObjectVersionForReplication permission might be missing for the source bucket\.  | 
| SrcKmsKeyInvalidState | The AWS KMS key for the source bucket isn't in a valid state\. Review and enable the required AWS KMS key\. For more information about managing AWS KMS keys, see [Key states of AWS KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/key-state.html) in the AWS Key Management Service Developer Guide\. | 
| SrcObjectNotEligible | Some objects aren't eligible for replication\. Either the object or the object tags don't match the replication configuration\. | 
| SrcVersionNotFound | Amazon S3 is unable to find the required object version in the source bucket for which metadata needs to be replicated\. | 

## Related topics<a name="replication-metrics-related-topics"></a>

[Setting up permissions](setting-repl-config-perm-overview.md)

[Troubleshooting replication](replication-troubleshoot.md)