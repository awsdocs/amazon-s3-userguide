# Using versioning in S3 buckets<a name="Versioning"></a>

Versioning in Amazon S3 is a means of keeping multiple variants of an object in the same bucket\. You can use the S3 Versioning feature to preserve, retrieve, and restore every version of every object stored in your buckets\. With versioning you can recover more easily from both unintended user actions and application failures\. After versioning is enabled for a bucket, if Amazon S3 receives multiple write requests for the same object simultaneously, it stores all of those objects\.

Versioning\-enabled buckets can help you recover objects from accidental deletion or overwrite\. For example, if you delete an object, Amazon S3 inserts a delete marker instead of removing the object permanently\. The delete marker becomes the current object version\. If you overwrite an object, it results in a new object version in the bucket\. You can always restore the previous version\. For more information, see [Deleting object versions from a versioning\-enabled bucket](DeletingObjectVersions.md)\. 

By default, S3 Versioning is disabled on buckets, and you must explicitly enable it\. For more information, see [Enabling versioning on buckets](manage-versioning-examples.md)\.

**Note**  
The SOAP API does not support S3 Versioning\. SOAP support over HTTP is deprecated, but it is still available over HTTPS\. New Amazon S3 features are not supported for SOAP\.
Normal Amazon S3 rates apply for every version of an object stored and transferred\. Each version of an object is the entire object; it is not just a diff from the previous version\. Thus, if you have three versions of an object stored, you are charged for three objects\. 

## Unversioned, versioning\-enabled, and versioning\-suspended buckets<a name="versioning-states"></a>

Buckets can be in one of three states: 
+ Unversioned \(the default\)
+ Versioning\-enabled
+ Versioning\-suspended

You enable and suspend versioning at the bucket level\. After you version\-enable a bucket, it can never return to an unversioned state\. But you can *suspend* versioning on that bucket\.

The versioning state applies to all \(never some\) of the objects in that bucket\. When you enable versioning in a bucket, all new objects are versioned and given a unique version ID\. Objects that already existed in the bucket at the time versioning was enabled will thereafter *always* be versioned and given a unique version ID when they are modified by future requests\. Note the following: 
+ Objects that are stored in your bucket before you set the versioning state have a version ID of `null`\. When you enable versioning, existing objects in your bucket do not change\. What changes is how Amazon S3 handles the objects in future requests\. For more information, see [Working with objects in a versioning\-enabled bucket](manage-objects-versioned-bucket.md)\.
+ The bucket owner \(or any user with appropriate permissions\) can suspend versioning to stop accruing object versions\. When you suspend versioning, existing objects in your bucket do not change\. What changes is how Amazon S3 handles objects in future requests\. For more information, see [Working with objects in a versioning\-suspended bucket](VersionSuspendedBehavior.md)\.

## Using S3 Versioning with S3 Lifecycle<a name="versioning-lifecycle"></a>

To customize your data retention approach and control storage costs, use object versioning with S3 Lifecycle\. For more information, see [Managing your storage lifecycle](object-lifecycle-mgmt.md)\. For information about creating S3 Lifecycle policies using the AWS Management Console, AWS CLI, AWS SDKs, or the REST API, see [Setting lifecycle configuration on a bucket](how-to-set-lifecycle-configuration-intro.md)\.

**Important**  
If you have an object expiration lifecycle policy in your unversioned bucket and you want to maintain the same permanent delete behavior when you enable versioning, you must add a noncurrent expiration policy\. The noncurrent expiration lifecycle policy manages the deletes of the noncurrent object versions in the version\-enabled bucket\. \(A version\-enabled bucket maintains one current, and zero or more noncurrent, object versions\.\) For more information, see [Setting lifecycle configuration on a bucket](how-to-set-lifecycle-configuration-intro.md)\.

For information about working with S3 Versioning, see the following topics\.

**Topics**
+ [Unversioned, versioning\-enabled, and versioning\-suspended buckets](#versioning-states)
+ [Using S3 Versioning with S3 Lifecycle](#versioning-lifecycle)
+ [How S3 Versioning works](versioning-workflows.md)
+ [Enabling versioning on buckets](manage-versioning-examples.md)
+ [Configuring MFA delete](MultiFactorAuthenticationDelete.md)
+ [Working with objects in a versioning\-enabled bucket](manage-objects-versioned-bucket.md)
+ [Working with objects in a versioning\-suspended bucket](VersionSuspendedBehavior.md)
+ [Working with archived objects](archived-objects.md)