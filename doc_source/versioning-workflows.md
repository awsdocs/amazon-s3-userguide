--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# How S3 Versioning works<a name="versioning-workflows"></a>

You can use S3 Versioning to keep multiple versions of an object in one bucket and prevent objects from being accidentally deleted or overwritten\. For example, if you delete an object, instead of removing it permanently, Amazon S3 inserts a delete marker, which becomes the current object version\. You can then restore the previous version\. For more information, see [Deleting object versions from a versioning\-enabled bucket](DeletingObjectVersions.md)\. If you overwrite an object, it results in a new object version in the bucket\. You can always restore the previous version\.

Each S3 bucket that you create has a *versioning* subresource associated with it\. \(For more information, see [Bucket configuration options](UsingBucket.md#bucket-config-options-intro)\.\) By default, your bucket is *unversioned*, and the versioning subresource stores the empty versioning configuration, as follows\.

```
<VersioningConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/"> 
</VersioningConfiguration>
```

To enable versioning, you can send a request to Amazon S3 with a versioning configuration that includes a status\. 

```
<VersioningConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/"> 
  <Status>Enabled</Status> 
</VersioningConfiguration>
```

To suspend versioning, you set the status value to `Suspended`\.

The bucket owner and all authorized IAM users can enable versioning\. The bucket owner is the AWS account that created the bucket \(the root account\)\. For more information about permissions, see [Identity and access management in Amazon S3](s3-access-control.md)\.

For more information about enabling and disabling S3 Versioning using the AWS Management Console, AWS Command Line Interface \(AWS CLI\), or REST API, see [Enabling versioning on buckets](manage-versioning-examples.md)\.

**Topics**
+ [Version IDs](#version-ids)
+ [Versioning workflows](#versioning-workflows-examples)

## Version IDs<a name="version-ids"></a>

If you enable versioning for a bucket, Amazon S3 automatically generates a unique version ID for the object that is being stored\. For example, in one bucket you can have two objects with the same key but different version IDs, such as `photo.gif` \(version 111111\) and `photo.gif` \(version 121212\)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_Enabled.png)

Regardless of whether you enable versioning, each object in your bucket has a version ID\. If you don't enable S3 Versioning, Amazon S3 sets the value of the version ID to null\. If you enable S3 Versioning, Amazon S3 assigns a version ID value for the object\. This value distinguishes it from other versions of the same key\.

When you enable S3 Versioning on an existing bucket, objects that are already stored in the bucket are unchanged\. The version IDs \(null\), contents, and permissions remain the same\. After you enable S3 Versioning for a bucket, each object that is added to the bucket gets a version ID, which distinguishes it from other versions of the same key\. 

Only Amazon S3 generates version IDs, and they cannot be edited\. Version IDs are Unicode, UTF\-8 encoded, URL\-ready, opaque strings that are no more than 1,024 bytes long\. The following is an example: `3/L4kqtJlcpXroDTDmJ+rmSpXd3dIbrHY+MTRCxf3vjVBH40Nr8X8gdRQBpUMLUo`\. \(For simplicity, the other examples in this topic use much shorter IDs\.\)

## Versioning workflows<a name="versioning-workflows-examples"></a>

When you `PUT` an object in a versioning\-enabled bucket, the noncurrent version is not overwritten\. The following figure shows that when a new version of `photo.gif` is `PUT` into a bucket that already contains an object with the same name, the original object \(ID = 111111\) remains in the bucket, Amazon S3 generates a new version ID \(121212\), and adds the newer version to the bucket\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_PUT_versionEnabled3.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/)

This functionality prevents you from accidentally overwriting or deleting objects and gives you the opportunity to retrieve a previous version of an object\. 

When you `DELETE` an object, all versions remain in the bucket and Amazon S3 inserts a delete marker, as shown in the following figure\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_DELETE_versioningEnabled.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/)

The delete marker becomes the current version of the object\. By default, `GET` requests retrieve the most recently stored version\. Performing a simple `GET Object` request when the current version is a delete marker returns a `404 Not Found` error, as shown in the following figure\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_DELETE_NoObjectFound2.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/)

However, you can `GET` a noncurrent version of an object by specifying its version ID\. In the following figure, you `GET` a specific object version, 111111\. Amazon S3 returns that object version even though it's not the current version\.

For more information, see [Retrieving object versions from a versioning\-enabled bucket](RetrievingObjectVersions.md)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_GET_Versioned3.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/)

You can permanently delete an object by specifying the version you want to delete\. Only the owner of an Amazon S3 bucket can permanently delete a version\. The following figure shows how `DELETE versionId` permanently deletes an object from a bucket and that Amazon S3 doesn't insert a delete marker\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_DELETE_versioningEnabled2.png)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/)![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/)

You can add additional security by configuring a bucket to enable MFA \(multi\-factor authentication\) delete\. When you do, the bucket owner must include two forms of authentication in any request to delete a version or change the versioning state of the bucket\. For more information, see [Configuring MFA delete](MultiFactorAuthenticationDelete.md)\.

**Important**  
If you notice a significant increase in the number of HTTP 503\-slow down responses received for Amazon S3 PUT or DELETE object requests to a bucket that has S3 Versioning enabled, you might have one or more objects in the bucket for which there are millions of versions\. For more information, see [Troubleshooting](troubleshooting.md)\.