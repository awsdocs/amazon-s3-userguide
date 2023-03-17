# Managing your replication<a name="manage-outposts-replication"></a>

This section describes additional replication configuration options that are available in S3 on Outposts, how to determine the replication status, and how to troubleshot replication\. For information about core replication configuration, see [Setting up replication](outposts-replication-how-setup.md)\.

**Topics**
+ [Monitoring progress with replication metrics](#outposts-enabling-replication-metrics)
+ [Getting replication status information](#outposts-replication-status)
+ [Troubleshooting replication](#outposts-replication-troubleshoot)
+ [Using EventBridge for S3 Replication on Outposts](outposts-replication-eventbridge.md)

## Monitoring progress with replication metrics<a name="outposts-enabling-replication-metrics"></a>

S3 Replication on Outposts provides detailed metrics for the replication rules in your replication configuration\. With replication metrics, you can monitor in 5\-minute intervals the progress of replication by tracking bytes pending replication, replication latency replication, and operations pending\. To assist in troubleshooting any configuration issues, you can also set up Amazon EventBridge to receive notifications about replication failures\.

When replication metrics are enabled, S3 Replication on Outposts publishes the following metrics to Amazon CloudWatch:
+ **Bytes Pending Replication** – The total number of bytes of objects that are pending replication for a given replication rule\.
+ **Replication Latency** – The maximum number of seconds by which the replication destination bucket is behind the source bucket for a given replication rule\.
+ **Operations Pending Replication** – The number of operations that are pending replication for a given replication rule\. Operations include objects, delete markers, and tags\.

**Note**  
S3 Replication on Outposts metrics are billed at the same rate as CloudWatch custom metrics\. For more information, see [CloudWatch pricing](https://aws.amazon.com/cloudwatch/pricing/)\.

## Getting replication status information<a name="outposts-replication-status"></a>

The replication status can help you determine the current state of an object that's being replicated by Amazon S3 on Outposts\. The replication status of a source object will return either `PENDING`, `COMPLETED`, or `FAILED`\. The replication status of a replica will return `REPLICA`\.

### Replication status overview<a name="outposts-replication-status-overview"></a>

In a replication scenario, you have a source bucket on which you configure replication and a destination bucket to which S3 on Outposts replicates objects\. When you request an object \(using `GetObject`\) or object metadata \(using `HeadObject`\) from these buckets, S3 on Outposts returns the `x-amz-replication-status` header in the response as follows: 
+ When you request an object from the source bucket, S3 on Outposts returns the `x-amz-replication-status` header if the object in your request is eligible for replication\. 

  For example, suppose that you specify the object prefix `TaxDocs` in your replication configuration to tell S3 on Outposts to replicate only objects with the key name prefix `TaxDocs`\. Any objects that you upload that have this key name prefix—for example, `TaxDocs/document1.pdf`—will be replicated\. For object requests with this key name prefix, S3 on Outposts returns the `x-amz-replication-status` header with one of the following values for the object's replication status: `PENDING`, `COMPLETED`, or `FAILED`\.
**Note**  
If object replication fails after you upload an object, you can't retry replication\. You must upload the object again\. Objects transition to a `FAILED` state for issues such as missing replication role permissions or missing bucket permissions\. For temporary failures, such as if a bucket or your Outpost is unavailable, the replication status doesn't transition to `FAILED`, but remains `PENDING`\. After the resource is back online, S3 on Outposts resumes replicating those objects\.
+ When you request an object from a destination bucket, if the object in your request is a replica that S3 on Outposts created, S3 on Outposts returns the `x-amz-replication-status` header with the value `REPLICA`\.

**Note**  
Before deleting an object from a source bucket that has replication enabled, check the object's replication status to ensure that the object has been replicated\. 

### Replication status if Amazon S3 replica modification sync on Outposts is enabled<a name="outposts-replication-status-sync"></a>

When your replication rules enable S3 on Outposts replica modification sync, replicas can report statuses other than `REPLICA`\. If metadata changes are in the process of replicating, the `x-amz-replication-status` header for the replica returns `PENDING`\. If replica modification sync fails to replicate metadata, the header for the replica returns `FAILED`\. If metadata is replicated correctly, the header for the replica returns the value `REPLICA`\.

## Troubleshooting replication<a name="outposts-replication-troubleshoot"></a>

If object replicas don't appear in the destination Amazon S3 on Outposts bucket after you configure replication, use these troubleshooting tips to identify and fix issues\.
+ The time it takes S3 on Outposts to replicate an object depends on several factors, including the distance between the source and destination Outposts, and the size of the object\.

  You can check the source object's replication status\. If the object's replication status is `PENDING`, S3 on Outposts hasn't completed the replication\. If the object's replication status is `FAILED`, check the replication configuration that you set on the source bucket\.
+ In the replication configuration on the source bucket, verify the following:
  + The access point Amazon Resource Name \(ARN\) of the destination bucket is correct\.
  + The key name prefix is correct\. For example, if you set the configuration to replicate objects with the prefix `Tax`, then only objects with key names such as `Tax/document1` or `Tax/document2` are replicated\. An object with the key name `document3` is not replicated\.
  + The status is `Enabled`\.
+ Verify that versioning hasn't been suspended on either bucket\. Both the source and destination buckets must have versioning enabled\.
+ If the destination bucket is owned by another AWS account, verify that the bucket owner has a bucket policy on the destination bucket that allows the source bucket owner to replicate objects\. For an example, see [Granting permissions when the source and destination Outposts buckets are owned by different AWS accounts](outposts-replication-prerequisites-config.md#outposts-rep-prethree)\.
+ If an object replica doesn't appear in the destination bucket, the following issues might prevent replication:
  + S3 on Outposts doesn't replicate an object in a source bucket that is a replica created by another replication configuration\. For example, if you set a replication configuration from bucket A to bucket B to bucket C, S3 on Outposts doesn't replicate object replicas in bucket B to bucket C\.

    If you want to replicate objects in bucket A to bucket B and bucket C, set multiple bucket destinations in different replication rules for your source bucket replication configuration\. For example, create two replication rules on source bucket A, with one rule to replicate to destination bucket B and the other rule to replicate to destination bucket C\. 
  + A source bucket owner can grant other AWS accounts permission to upload objects\. By default, the source bucket owner doesn't have permissions for the objects created by other accounts\. The replication configuration replicates only the objects for which the source bucket owner has access permissions\. To avoid replication issues, the source bucket owner can grant other AWS accounts permissions to create objects conditionally, requiring explicit access permissions on those objects\. For an example policy, see [Grant cross\-account permissions to upload objects while ensuring that the bucket owner has full control](example-bucket-policies.md#example-bucket-policies-acl-2)\.
+ Suppose that in the replication configuration, you add a rule to replicate a subset of objects that have a specific tag\. In this case, you must assign the specific tag key and value at the time that the object is created in order for S3 on Outposts to replicate the object\. If you first create an object and then add the tag to that existing object, S3 on Outposts doesn't replicate the object\.
+ Replication fails if the bucket policy denies access to the replication role for any of the following actions:

  Source bucket:

  ```
  1. "s3-outposts:GetObjectVersionForReplication",
  2. "s3-outposts:GetObjectVersionTagging"
  ```

  Destination buckets:

  ```
  1. "s3-outposts:ReplicateObject",
  2. "s3-outposts:ReplicateDelete",
  3. "s3-outposts:ReplicateTags"
  ```
+ Amazon EventBridge can notify you when objects don't replicate to their destination Outposts\. For more information, see [Using EventBridge for S3 Replication on Outposts](outposts-replication-eventbridge.md)\.