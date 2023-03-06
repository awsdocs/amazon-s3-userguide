# Amazon S3 replication failure reasons<a name="replication-failure-codes"></a>

The following table lists Amazon S3 Replication failure reasons\. These can be viewed as the **failureReason** with Amazon S3 event notifications through Amazon Simple Queue Service \(Amazon SQS\), Amazon Simple Notification Service \(Amazon SNS\), or AWS Lambda\. For more information, see [Amazon S3 Event Notifications](NotificationHowTo.md)\.

These failure reasons can also be viewed in a S3 Batch Replication completion report\. For more information, see [Batch Replication completion report](s3-batch-replication-batch.md#batch-replication-completion-report)\.


| Replication failure reason | Description | 
| --- | --- | 
| AssumeRoleNotPermitted | Amazon S3 can not assume the IAM role specified in replication configuration or the Batch Operations job\. | 
| DstBucketNotFound | Amazon S3 is unable to find the destination bucket specified in the replication configuration\. | 
| DstBucketObjectLockConfigMissing | To replicate objects from a source bucket with Object Lock enabled the destination must also have Object Lock enabled\. Error indicates Object Lock may not be enabled in the destination bucket\. For more information, see [[Managing Object Lock](object-lock-managing.md)Managing Object Lock](object-lock-managing.md)\. | 
| DstBucketUnversioned | Versioning is not enabled on S3 destination bucket\. Enable versioning on destination bucket to replicate objects with S3 Replication\. | 
| DstDelObjNotPermitted | Amazon S3 is unable to replicate deletes to the destination bucket\. The s3:ReplicateDelete permission may be missing for the destination bucket\. | 
| DstKmsKeyInvalidState | The AWS Key Management Service key for the destination bucket is in an invalid state\. Review and enable the required AWS KMS key\. For more information on managing AWS KMS keys, see [Key states of AWS KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/key-state.html) in the AWS Key Management Service Developer Guide\. | 
| DstKmsKeyNotFound | The AWS Key Management Service key configured in the replication configuration does not exist\. | 
| DstMultipartCompleteNotPermitted | Amazon S3 is unable to complete multipart upload of objects in the destination bucket\. The s3:ReplicateObject permission may be missing for the destination bucket\.  | 
| DstMultipartInitNotPermitted | Amazon S3 is unable to initiate multipart upload of objects to the destination bucket\. The s3:ReplicateObject permission may be missing for the destination bucket\.  | 
| DstMultipartPartUploadNotPermitted | Amazon S3 is unable to upload multipart objects in the destination bucket\. The s3:ReplicateObject permission may be missing for the destination bucket\.  | 
| DstObjectHardDeleted | S3 Batch Replication does not support re\-replicating objects deleted with the version ID of the object from the destination bucket\. This error is specific to Batch Replication\. | 
| DstPutAclNotPermitted | Amazon S3 is unable to replicate object ACL to the destination bucket\. The s3:ReplicateObject permission may be missing for the destination bucket\. | 
| DstPutLegalHoldNotPermitted | Amazon S3 is unable to put an Object Lock legal hold on the destination object while replicating immutable objects\. The s3:PutObjectLegalHold permission may be missing for the destination bucket\. For more information, see [Legal holds](object-lock-overview.md#object-lock-legal-holds)\. | 
|  DstPutObjectNotPermitted | Amazon S3 is unable to replicate objects to the destination bucket\. The s3:ReplicateObject or s3:ObjectOwnerOverrideToBucketOwner permissions might be missing for the destination bucket\. | 
| DstPutTaggingNotPermitted | Amazon S3 is unable to replicate object tags to the destination bucket\. The s3:ReplicateObject permission may be missing for the destination bucket\.  | 
| DstVersionNotFound  | Amazon S3 is unable to find the required object version in the destination bucket for which metadata needs to be replicated\.  | 
| InitiateReplicationNotPermitted | Initiate replication on object failed\. The s3:InitiateReplication permission may be missing for the Batch Operations job\. This error is specific to Batch Replication\. | 
| SrcBucketInvalidRegion | Source bucket is not in same AWS Region as specified by the Batch Operations job\. This error is specific to Batch Replication\. | 
| SrcBucketNotFound | Amazon S3 is unable to find the source bucket\. | 
| SrcBucketReplicationConfigMissing | Replication configuration not found for source bucket\. | 
| SrcGetAclNotPermitted | Amazon S3 is unable to access the object in the source bucket for replication\. The s3:GetObjectVersionAcl permission may be missing for the source bucket object\.  | 
| SrcGetObjectNotPermitted | Amazon S3 is unable to access the object in the source bucket for replication\. The s3:GetObjectVersionForReplication permission may be missing for the source bucket\.  | 
| SrcGetTaggingNotPermitted | Amazon S3 is unable to access object tag information from source bucket\. The s3:GetObjectVersionTagging permission may be missing for the source bucket\. | 
| SrcHeadObjectNotPermitted | Amazon S3 is unable to retrieve object metadata from source bucket\. The s3:GetObjectVersionForReplication permission may be missing for the source bucket\.  | 
| SrcKmsKeyInvalidState | The AWS Key Management Service key for the source bucket is in invalid state\. Review and enable the required AWS KMS key\. For more information on managing AWS KMS keys, see [Key states of AWS KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/key-state.html) in the AWS Key Management Service Developer Guide\. | 
| SrcObjectNotEligible | Object is not eligible for replication\. Object or object tags do not match replication configuration\. | 
| SrcVersionNotFound | Amazon S3 is unable to find the required object version in the source bucket for which metadata needs to be replicated\. | 

## Related topics<a name="replication-metrics-related-topics"></a>

[Setting up permissions](setting-repl-config-perm-overview.md)

[Troubleshooting replication](replication-troubleshoot.md)