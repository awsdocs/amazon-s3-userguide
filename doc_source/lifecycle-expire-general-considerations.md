# Expiring objects<a name="lifecycle-expire-general-considerations"></a>

When an object reaches the end of its lifetime based on its lifecycle configuration, Amazon S3 takes an action based on which state the bucket is in\.
+ **Non\-versioned bucket** – Amazon S3 queues the object for removal and removes it asynchronously, permanently removing the object\. 
+ **Versioning\-enabled bucket** – If the current object version is not a delete marker, Amazon S3 adds a delete marker with a unique version ID\. This makes the current version noncurrent, and the delete marker the current version\. 
+ **Versioning\-suspended bucket** – Amazon S3 creates a delete marker with null as the version ID\. This delete marker replaces any object version with a null version ID in the version hierarchy, which effectively deletes the object\. 

For a versioned bucket \(that is, versioning\-enabled or versioning\-suspended\), there are several considerations that guide how Amazon S3 handles the Expiration action\. For versioning\-enabled or versioning\-suspended buckets, the following applies:
+ Object expiration applies only to an object's current version \(it has no impact on noncurrent object versions\)\.
+ Amazon S3 doesn't take any action if there are one or more object versions and the delete marker is the current version\.
+ If the current object version is the only object version and it is also a delete marker \(also referred as an *expired object delete marker*, where all object versions are deleted and you only have a delete marker remaining\), Amazon S3 removes the expired object delete marker\. You can also use the expiration action to direct Amazon S3 to remove any expired object delete markers\. For example, see [Example 7: Removing expired object delete markers](lifecycle-configuration-examples.md#lifecycle-config-conceptual-ex7)\.

For more information, see [Using versioning in S3 buckets](Versioning.md)\.

## How to find when objects will expire<a name="lifecycle-expire-when"></a>

To find when an object is scheduled to expire, use the [HEAD Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectHEAD.html) or [GET Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGET.html) API operation\. These API operations return response headers that provide the date and time at which the object is no longer cacheable\. 

**Note**  
There may be a delay between the expiration date and the date at which Amazon S3 removes an object\. You are not charged for expiration or the storage time associated with an object that has expired\. 

## Minimum storage duration charge<a name="lifecycle-expire-minimum-storage"></a>

If you create an S3 Lifecycle expiration rule that causes objects that have been in S3 Standard\-IA or S3 One Zone\-IA storage for less than 30 days to expire, you are charged for 30 days\. If you create a Lifecycle expiration rule that causes objects that have been in S3 Glacier Flexible Retrieval storage for less than 90 days to expire, you are charged for 90 days\. If you create a Lifecycle expiration rule that causes objects that have been in S3 Glacier Deep Archive storage for less than 180 days to expire, you are charged for 180 days\.

For more information, see [Amazon S3 pricing](https://aws.amazon.com/s3/pricing/)\.