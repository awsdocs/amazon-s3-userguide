# Copy objects<a name="batch-ops-copy-object"></a>

The **Copy** operation copies each object that is specified in the manifest\. You can copy objects to a bucket in the same AWS Region or to a bucket in a different Region\. S3 Batch Operations supports most options available through Amazon S3 for copying objects\. These options include setting object metadata, setting permissions, and changing an object's storage class\. 

You can also use the Copy operation to copy existing unencrypted objects and write them back to the same bucket as encrypted objects\. For more information, see [Encrypting objects with Amazon S3 Batch Operations](http://aws.amazon.com/blogs/storage/encrypting-objects-with-amazon-s3-batch-operations/)\.

For more information about copying objects in Amazon S3 and the required and optional parameters, see [Copying objects](copy-object.md) in this guide and [CopyObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html) in the *Amazon Simple Storage Service API Reference*\.

## Restrictions and limitations<a name="batch-ops-copy-object-restrictions"></a>
+ All source objects must be in one bucket\.
+ All destination objects must be in one bucket\.
+ You must have read permissions for the source bucket and write permissions for the destination bucket\.
+ Objects to be copied can be up to 5 GB in size\.
+ If you try to copy objects from the S3 Glacier storage classes to the S3 Standard storage class, you need to first restore these objects\. For more information, see [Restoring an archived object](restoring-objects.md)\.
+ Copy jobs must be created in the destination Region, which is the Region you intend to copy the objects to\.
+ All Copy options are supported except for conditional checks on ETags and server\-side encryption with customer\-provided encryption keys \(SSE\-C\)\.
+ If the buckets are unversioned, you will overwrite objects with the same key names\.
+ Objects are not necessarily copied in the same order as they appear in the manifest\. For versioned buckets, if preserving current/non\-current version order is important, you should copy all noncurrent versions first\. Then, after the first job is complete, copy the current versions in a subsequent job\. 
+ Copying objects to the Reduced Redundancy Storage \(RRS\) class is not supported\.