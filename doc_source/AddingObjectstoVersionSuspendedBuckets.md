--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Adding objects to versioning\-suspended buckets<a name="AddingObjectstoVersionSuspendedBuckets"></a>

You can add objects to versioning\-suspended buckets to create the object with a null version ID or overwrite any object version with a matching version ID\.

After you suspend versioning on a bucket, Amazon S3 automatically adds a `null` version ID to every subsequent object stored thereafter \(using `PUT`, `POST`, or `COPY`\) in that bucket\.

The following figure shows how Amazon S3 adds the version ID of `null` to an object when it is added to a version\-suspended bucket\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_PUT_versionSuspended.png)

If a null version is already in the bucket and you add another object with the same key, the added object overwrites the original null version\. 

If there are versioned objects in the bucket, the version you `PUT` becomes the current version of the object\. The following figure shows how adding an object to a bucket that contains versioned objects does not overwrite the object already in the bucket\. In this case, version 111111 was already in the bucket\. Amazon S3 attaches a version ID of null to the object being added and stores it in the bucket\. Version 111111 is not overwritten\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_PUT_versionSuspended3.png)

If a null version already exists in a bucket, the null version is overwritten, as shown in the following figure\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_PUT_versionSuspended4.png)

Although the key and version ID \(`null`\) of the null version are the same before and after the `PUT`, the contents of the null version originally stored in the bucket is replaced by the contents of the object `PUT` into the bucket\.