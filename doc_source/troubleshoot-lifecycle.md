# Troubleshoot Amazon S3 Lifecycle issues<a name="troubleshoot-lifecycle"></a>

The following information can help you troubleshoot common issues with Amazon S3 Lifecycle rules\.

**Topics**
+ [I ran a list operation on my bucket and saw objects that I thought were expired or transitioned by a lifecycle rule\.](#troubleshoot-lifecycle-1)
+ [How do I monitor the progress of my lifecycle rule to verify that it's active?](#troubleshoot-lifecycle-2)
+ [My S3 object count still increases, even after setting up lifecycle rules on a version\-enabled bucket\.](#troubleshoot-lifecycle-3)
+ [How do I empty my S3 bucket by using lifecycle rules?](#troubleshoot-lifecycle-4)
+ [My Amazon S3 bill increased after transitioning objects to a lower\-cost storage class\.](#troubleshoot-lifecycle-5)
+ [I’ve updated my bucket policy, but my S3 objects are still being deleted by expired lifecycle rules\.](#troubleshoot-lifecycle-6)
+ [Can I recover S3 objects that are expired by S3 Lifecycle rules?](#troubleshoot-lifecycle-7)

## I ran a list operation on my bucket and saw objects that I thought were expired or transitioned by a lifecycle rule\.<a name="troubleshoot-lifecycle-1"></a>

S3 Lifecycle [object transitions](https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-transition-general-considerations.html) and [object expirations](https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-expire-general-considerations.html) are asynchronous operations\. Therefore, there might be a delay between the time that the objects are eligible for expiration or transition and the time that they are actually transitioned or expired\. Changes in billing are applied as soon as the lifecycle rule is satisfied, even if the action isn't complete\. The exception to this behavior is if you have a lifecycle rule set to transition to the S3 Intelligent\-Tiering storage class\. In that case, billing changes don't occur until the object has transitioned to S3 Intelligent\-Tiering\. For more information about changes in billing, see [Setting lifecycle configuration on a bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/how-to-set-lifecycle-configuration-intro.html)\.

**Note**  
Amazon S3 doesn’t transition objects that are smaller than 128 KB from the S3 Standard or S3 Standard\-IA storage class to the S3 Intelligent\-Tiering, S3 Standard\-IA, or S3 One Zone\-IA storage class\.

## How do I monitor the progress of my lifecycle rule to verify that it's active?<a name="troubleshoot-lifecycle-2"></a>

To view the progress of \(or to monitor changes made by\) any active lifecycle rules, [use the Storage Lens dashboard](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage_lens_basics_metrics_recommendations.html#storage_lens_basics_dashboards)\. With the dashboard, you can view the following metrics, which monitor the object count or size\.
+ **Current version bytes**
+ **Current version object count**
+ **Noncurrent version bytes**
+ **Noncurrent version object count**
+ **Delete marker object count**
+ **Delete marker storage bytes**
+ **Incomplete multipart upload bytes**
+ **Incomplete multipart upload object count**

You can also use the following features to monitor your lifecycle rules:
+ [Amazon S3 Inventory](https://docs.aws.amazon.com/AmazonS3/latest/userguide/configure-inventory.html) – You can use S3 Inventory to generate the list of prefixes or objects for the Amazon S3 bucket \(in CSV\), Apache optimized row columnar \(ORC\), or Apache Parquet format for auditing purposes\. Depending on your use case, you can also query S3 Inventory in standard SQL with Amazon Athena\.
+ [S3 Event Notifications](https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-configure-notification.html) – You can set up event notifications so that you're notified of any lifecycle expiration or transition events\.
+ S3 server access logs – You can enable server access logs for the S3 bucket to capture lifecycle\-related actions, such as object transitions to another storage class and object expirations\. For more information, see [Lifecycle and logging](https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-and-other-bucket-config.html#lifecycle-general-considerations-logging)\.

## My S3 object count still increases, even after setting up lifecycle rules on a version\-enabled bucket\.<a name="troubleshoot-lifecycle-3"></a>

When an object is set to be expired in a [versioning\-enabled bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Versioning.html#versioning-states), the object isn’t completely deleted from the bucket\. Instead, a [delete marker](https://docs.aws.amazon.com/AmazonS3/latest/userguide/DeleteMarker.html) is created as the newest version of the object\. Delete markers are still counted as objects\. Therefore, if a lifecycle rule is created to expire only the current versions, then the object count in the S3 bucket actually increases instead of going down\.

For example, let's say an S3 bucket is versioning\-enabled with 100 objects, and a lifecycle rule is set to expire current versions of the object after 7 days\. After the seventh day, the object count increases to 200 because 100 delete markers are created in addition to the 100 original objects, which are now the noncurrent versions\. For more information about S3 Lifecycle configuration rule actions for versioning\-enabled buckets, see [Setting lifecycle configuration on a bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/how-to-set-lifecycle-configuration-intro.html)\.

To permanently remove objects, add an additional lifecycle configuration to delete the previous versions of the objects, expired delete markers, and incomplete multipart uploads\. For instructions on how to create new lifecycle rules, see [Setting lifecycle configuration on a bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/how-to-set-lifecycle-configuration-intro.html)\.

**Note**  
Amazon S3 rounds the transition or expiration date of an object to midnight UTC the next day\. For more information, see [Lifecycle rules: Based on an object's age](https://docs.aws.amazon.com/AmazonS3/latest/dev/intro-lifecycle-rules.html#intro-lifecycle-rules-number-of-days)\.
For S3 objects that are protected by Object Lock, current versions are not permanently deleted\. Instead, a delete marker is added to the objects, making them noncurrent\. Noncurrent versions are then preserved and are not permanently expired\.

## How do I empty my S3 bucket by using lifecycle rules?<a name="troubleshoot-lifecycle-4"></a>

S3 Lifecycle rules are an effective tool to [empty an S3 bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/empty-bucket.html) with millions of objects\. To delete a large number of objects from your S3 bucket, make sure to use these four pairs of lifecycle rules:
+ **Expire current versions of objects** and **Permanently delete previous versions of objects**
+ **Delete expired delete markers** and **Delete incomplete multipart uploads**

For steps on how to create a lifecycle configuration rule, see [Setting lifecycle configuration on a bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/how-to-set-lifecycle-configuration-intro.html)\.

**Note**  
For S3 objects that are protected by Object Lock, current versions are not permanently deleted\. Instead, a delete marker is added to the objects, making them noncurrent\. Noncurrent versions are then preserved and are not permanently expired\.

## My Amazon S3 bill increased after transitioning objects to a lower\-cost storage class\.<a name="troubleshoot-lifecycle-5"></a>

There are several reasons that your bill might increase after transitioning objects to a lower\-cost storage class: 
+ S3 Glacier overhead charges for small objects

  For each object that is transitioned to S3 Glacier Flexible Retrieval or S3 Glacier Deep Archive, a total overhead of 40 KB is associated with this storage update\. As part of the 40 KB overhead, 8 KB is used to store metadata and the name of the object\. This 8 KB is charged according to S3 Standard rates\. The remaining 32 KB is used for indexing and related metadata\. This 32 KB is charged according to S3 Glacier Flexible Retrieval or S3 Glacier Deep Archive pricing\.

  Therefore, if you're storing many smaller sized objects, we don't recommend using lifecycle transitions\. Instead, to reduce any overhead charges, consider aggregating many smaller objects into a smaller number of large objects before storing them in Amazon S3\. For more information about cost considerations, see [Transitioning to the S3 Glacier Flexible Retrieval and S3 Glacier Deep Archive storage classes \(object archival\)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-transition-general-considerations.html#before-deciding-to-archive-objects)\.
+ Minimum storage charges

  Some S3 storage classes have minimum storage\-duration requirements\. Objects that are deleted, overwritten, or transitioned from those classes before the minimum duration is satisfied are charged a prorated early transition or deletion fee\. These minimum storage\-duration requirements are as follows: 
  + S3 Standard\-IA and S3 One Zone\-IA – 30 days
  + S3 Glacier Flexible Retrieval and S3 Glacier Instant Retrieval – 90 days
  + S3 Glacier Deep Archive – 180 days

  For more information about these requirements, see the *Constraints* section of [Transitioning objects using S3 Lifecycle](https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-transition-general-considerations.html)\. For general S3 pricing information, see [Amazon S3 pricing](https://aws.amazon.com/s3/pricing/) and the [AWS Pricing Calculator](https://calculator.aws/#/addService/S3)\.
+ Lifecycle transition costs

  Each time an object is transitioned to a different storage class by a lifecycle rule, Amazon S3 counts that transition as one transition request\. The costs for these transition requests are in addition to the costs of these storage classes\. If you plan to transition a large number of objects, consider the request costs when transitioning to a lower tier\. For more information, see [Amazon S3 pricing](https://aws.amazon.com/s3/pricing/)\.

## I’ve updated my bucket policy, but my S3 objects are still being deleted by expired lifecycle rules\.<a name="troubleshoot-lifecycle-6"></a>

`Deny` statements in a bucket policy don't prevent the expiration of the objects defined in a lifecycle rule\. Lifecycle actions \(such as transitions or expirations\) don't use the S3 `DeleteObject` operation\. Instead, S3 Lifecycle actions are performed by using internal S3 endpoints\. \(For more information, see [Lifecycle and logging](https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-and-other-bucket-config.html#lifecycle-general-considerations-logging)\.\) 

To prevent your lifecycle rule from taking any action, you must edit, delete, or [disable the rule](https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-configuration-examples.html#lifecycle-config-conceptual-ex2)\.

## Can I recover S3 objects that are expired by S3 Lifecycle rules?<a name="troubleshoot-lifecycle-7"></a>

The only way to recover objects that are expired by S3 Lifecycle is through versioning, which must be in place before the objects become eligible for expiration\. You cannot undo the expiration operations that are performed by lifecycle rules\. If objects are permanently deleted by the S3 Lifecycle rules that are in place, you cannot recover these objects\. To enable versioning on a bucket, see [Using versioning in S3 buckets](Versioning.md)\.

If you have applied versioning to the bucket and the noncurrent versions of the objects are still intact, you can [restore previous versions of the expired objects](https://docs.aws.amazon.com/AmazonS3/latest/userguide/RestoringPreviousVersions.html)\. For more information about the behavior of S3 Lifecycle rule actions and versioning states, see the *Lifecycle actions and bucket versioning state* table in [Elements to describe lifecycle actions](https://docs.aws.amazon.com/AmazonS3/latest/userguide/intro-lifecycle-rules.html#non-current-days-calculations)\.

**Note**  
If the S3 bucket is protected by [AWS Backup](https://docs.aws.amazon.com/aws-backup/latest/devguide/s3-backups.html) or [S3 Replication](https://docs.aws.amazon.com/AmazonS3/latest/userguide/replication.html), you might also be able to use these features to recover your expired objects\.