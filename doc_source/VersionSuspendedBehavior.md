# Working with objects in a versioning\-suspended bucket<a name="VersionSuspendedBehavior"></a>

In Amazon S3, you can suspend versioning to stop accruing new versions of the same object in a bucket\. You might do this because you only want a single version of an object in a bucket\. Or, you might not want to accrue charges for multiple versions\. 

When you suspend versioning, existing objects in your bucket do not change\. What changes is how Amazon S3 handles objects in future requests\. The topics in this section explain various object operations in a versioning\-suspended bucket, including adding, retrieving, and deleting objects\.

For more information about S3 Versioning, see [Using versioning in S3 buckets](Versioning.md)\. For more information about retrieving object versions, see [Retrieving object versions from a versioning\-enabled bucket](RetrievingObjectVersions.md)\.

**Topics**
+ [Adding objects to versioning\-suspended buckets](AddingObjectstoVersionSuspendedBuckets.md)
+ [Retrieving objects from versioning\-suspended buckets](RetrievingObjectsfromVersioningSuspendedBuckets.md)
+ [Deleting objects from versioning\-suspended buckets](DeletingObjectsfromVersioningSuspendedBuckets.md)