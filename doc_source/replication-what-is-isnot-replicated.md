# What does Amazon S3 replicate?<a name="replication-what-is-isnot-replicated"></a>

Amazon S3 replicates only specific items in buckets that are configured for replication\. 

**Topics**
+ [What is replicated with replication configurations?](#replication-what-is-replicated)
+ [What isn't replicated with replication configurations?](#replication-what-is-not-replicated)

## What is replicated with replication configurations?<a name="replication-what-is-replicated"></a>

By default, Amazon S3 replicates the following:
+ Objects created after you add a replication configuration\.
+ Unencrypted objects\. 
+ Objects encrypted at rest under an Amazon S3 managed key \(SSE\-S3\) or a KMS key stored in AWS Key Management Service \(SSE\-KMS\)\. 

  To replicate objects encrypted with a KMS key stored in AWS KMS, you must explicitly enable the option\. The replicated copy of the object is encrypted using the same type of server\-side encryption that was used for the source object\. For more information about server\-side encryption, see [Protecting data using server\-side encryption](serv-side-encryption.md)\.
+ Object metadata from the source objects to the replicas\. For information about replicating metadata from the replicas to the source objects, see [Replicating metadata changes with Amazon S3 replica modification sync](replication-for-metadata-changes.md)\.
+ Only objects in the source bucket for which the bucket owner has permissions to read objects and access control lists \(ACLs\)\. 

  For more information about resource ownership, see [Amazon S3 bucket and object ownership](access-control-overview.md#about-resource-owner)\.
+ Object ACL updates, unless you direct Amazon S3 to change the replica ownership when source and destination buckets aren't owned by the same accounts\. 

  For more information, see [Changing the replica owner](replication-change-owner.md)\. 

  It can take a while until Amazon S3 can bring the two ACLs in sync\. This change in ownership applies only to objects created after you add a replication configuration to the bucket\.
+  Object tags, if there are any\.
+ S3 Object Lock retention information, if there is any\. 

  When Amazon S3 replicates objects that have retention information applied, it applies those same retention controls to your replicas, overriding the default retention period configured on your destination buckets\. If you don't have retention controls applied to the objects in your source bucket, and you replicate into destination buckets that have a default retention period set, the destination bucket's default retention period is applied to your object replicas\. For more information, see [Using S3 Object Lock](object-lock.md)\.

### How delete operations affect replication<a name="replication-delete-op"></a>

If you delete an object from the source bucket, the following actions occur by default:
+ If you make a DELETE request without specifying an object version ID, Amazon S3 adds a delete marker\. Amazon S3 deals with the delete marker as follows:
  + If you are using the latest version of the replication configuration \(that is, you specify the `Filter` element in a replication configuration rule\), Amazon S3 does not replicate the delete marker by default\. However, you can add *delete marker replication* to non\-tag\-based rules\. For more information, see [Replicating delete markers between buckets](delete-marker-replication.md)\.
  + If you don't specify the `Filter` element, Amazon S3 assumes that the replication configuration is version V1, and it replicates delete markers that resulted from user actions\. However, if Amazon S3 deletes an object due to a lifecycle action, the delete marker is not replicated to the destination buckets\.
+ If you specify an object version ID to delete in a DELETE request, Amazon S3 deletes that object version in the source bucket\. But it doesn't replicate the deletion in the destination buckets\. In other words, it doesn't delete the same object version from the destination buckets\. This protects data from malicious deletions\. 

## What isn't replicated with replication configurations?<a name="replication-what-is-not-replicated"></a>

By default, Amazon S3 doesn't replicate the following:
+ Objects in the source bucket that are replicas that were created by another replication rule\. For example, suppose you configure replication where bucket A is the source and bucket B is the destination\. Now suppose that you add another replication configuration where bucket B is the source and bucket C is the destination\. In this case, objects in bucket B that are replicas of objects in bucket A are not replicated to bucket C\. 

  To replicate objects that are replicas, use Batch Replication\. Learn more about configuring Batch Replication at [Replicate existing objects](s3-batch-replication-batch.md)\.
+ Objects in the source bucket that have already been replicated to a different destination\. For example, if you change the destination bucket in an existing replication configuration, Amazon S3 won't replicate the objects again\.

  To replicate previously replicated objects, use Batch Replication\. Learn more about configuring Batch Replication at [Replicate existing objects](s3-batch-replication-batch.md)\.
+ Objects created with server\-side encryption using customer\-provided encryption keys \(SSE\-C\)\.
+ By default, when replicating from a different AWS account, delete markers added to the source bucket are not replicated\.

  For information about how to replicate delete markers, see [Replicating delete markers between buckets](delete-marker-replication.md)\.
+ Objects that are stored in the S3 Glacier Flexible Retrieval or S3 Glacier Deep Archive storage class\. 

  To learn more about the Amazon S3 Glacier service, see the [Amazon S3 Glacier Developer Guide](https://docs.aws.amazon.com/amazonglacier/latest/dev/)\.
+ Objects in the source bucket that the bucket owner doesn't have sufficient permissions to replicate\. 

  For information about how an object owner can grant permissions to a bucket owner, see [Granting cross\-account permissions to upload objects while ensuring the bucket owner has full control](example-bucket-policies.md#example-bucket-policies-use-case-8)\.
+ Updates to bucket\-level subresources\. 

  For example, if you change the lifecycle configuration or add a notification configuration to your source bucket, these changes are not applied to the destination bucket\. This feature makes it possible to have different configurations on source and destination buckets\. 
+ Actions performed by lifecycle configuration\. 

  For example, if lifecycle configuration is enabled only on your source bucket, Amazon S3 creates delete markers for expired objects but doesn't replicate those markers\. If you want the same lifecycle configuration applied to both the source and destination buckets, enable the same lifecycle configuration on both\. For more information about lifecycle configuration, see [Managing your storage lifecycle](object-lifecycle-mgmt.md)\.