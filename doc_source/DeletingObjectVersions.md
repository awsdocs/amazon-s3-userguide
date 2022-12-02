# Deleting object versions from a versioning\-enabled bucket<a name="DeletingObjectVersions"></a>

You can delete object versions from Amazon S3 buckets whenever you want\. You can also define lifecycle configuration rules for objects that have a well\-defined lifecycle to request Amazon S3 to expire current object versions or permanently remove noncurrent object versions\. When your bucket is version\-enabled or versioning is suspended, the lifecycle configuration actions work as follows:
+ The `Expiration` action applies to the current object version\. Instead of deleting the current object version, Amazon S3 retains the current version as a noncurrent version by adding a *delete marker*, which then becomes the current version\.
+ The `NoncurrentVersionExpiration` action applies to noncurrent object versions, and Amazon S3 permanently removes these object versions\. You cannot recover permanently removed objects\.

For more information about S3 Lifecycle, see [Managing your storage lifecycle](object-lifecycle-mgmt.md)\.

To see how many current and noncurrent object versions that your buckets have, you can use Amazon S3 Storage Lens metrics\. S3 Storage Lens is a cloud\-storage analytics feature that you can use to gain organization\-wide visibility into object\-storage usage and activity\. For more information, see [ Using S3 Storage Lens to optimize your storage costs](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-lens-optimize-storage.html?icmpid=docs_s3_user_guide_DeletingObjectVersions.html)\. For a complete list of metrics, see [ S3 Storage Lens metrics glossary](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage_lens_metrics_glossary.html?icmpid=docs_s3_user_guide_replication.html)\.

## Delete request use cases<a name="delete-request-use-cases"></a>

A `DELETE` request has the following use cases:
+ When versioning is enabled, a simple `DELETE` cannot permanently delete an object\. Instead, Amazon S3 inserts a delete marker in the bucket, and that marker becomes the current version of the object with a new ID\. 

  When you try to `GET` an object whose current version is a delete marker, Amazon S3 behaves as though the object has been deleted \(even though it has not been erased\) and returns a 404 error\. For more information, see [Working with delete markers](DeleteMarker.md)\.

  The following figure shows that a simple `DELETE` does not actually remove the specified object\. Instead, Amazon S3 inserts a delete marker\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_DELETE_versioningEnabled.png)
+ To delete versioned objects permanently, you must use `DELETE Object versionId`\.

  The following figure shows that deleting a specified object version permanently removes that object\.  
![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_DELETE_versioningEnabled2.png)

## To delete object versions<a name="delete-object-version"></a>

You can delete object versions in Amazon S3 using the console, AWS SDKs, the REST API, or the AWS Command Line Interface\.

### Using the S3 console<a name="deleting-object-versions"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that contains the object\.

1. In the **Objects** list, choose the name of the object\.

1. Choose **Versions**\.

   Amazon S3 shows all the versions for the object\.

1. Select the check box next to the **Version ID** for the versions that you want to permanently delete\.

1. Choose **Delete**\.

1. In **Permanently delete objects?**, enter **permanently delete**\.
**Warning**  
When you permanently delete an object version, the action cannot be undone\.

1. Choose **Delete objects**\.

   Amazon S3 deletes the object version\.

### Using the AWS SDKs<a name="delete-obj-version-version-enabled-bucket-sdks"></a>

For examples of deleting objects using the AWS SDKs for Java, \.NET, and PHP, see [Deleting Amazon S3 objects](DeletingObjects.md)\. The examples for deleting objects in nonversioned and versioning\-enabled buckets are the same\. However, for versioning\-enabled buckets, Amazon S3 assigns a version number\. Otherwise, the version number is null\. 

For information about using other AWS SDKs, see the [AWS Developer Center](https://aws.amazon.com/code/)\. 

------
#### [ Python ]

For instructions on how to create and test a working sample, see [Using the AWS SDK for Python \(Boto\)](UsingTheBotoAPI.md)\. 

The following Python code example permanently deletes a versioned object by deleting all of its versions\.

```
def permanently_delete_object(bucket, object_key):
    """
    Permanently deletes a versioned object by deleting all of its versions.

    Usage is shown in the usage_demo_single_object function at the end of this module.

    :param bucket: The bucket that contains the object.
    :param object_key: The object to delete.
    """
    try:
        bucket.object_versions.filter(Prefix=object_key).delete()
        logger.info("Permanently deleted all versions of object %s.", object_key)
    except ClientError:
        logger.exception("Couldn't delete all versions of %s.", object_key)
        raise
```

------

### Using the REST API<a name="delete-obj-version-enabled-bucket-rest"></a>

**To a delete a specific version of an object**
+ In a `DELETE`, specify a version ID\.

**Example — Deleting a specific version**  
The following example deletes version `UIORUnfnd89493jJFJ` of `photo.gif`\.  

```
1. DELETE /photo.gif?versionId=UIORUnfnd89493jJFJ HTTP/1.1 
2. Host: bucket.s3.amazonaws.com
3. Date: Wed, 12 Oct 2009 17:50:00 GMT
4. Authorization: AWS AKIAIOSFODNN7EXAMPLE:xQE0diMbLRepdf3YB+FIEXAMPLE=
5. Content-Type: text/plain
6. Content-Length: 0
```

### Using the AWS CLI<a name="delete-obj-version-enabled-bucket-cli"></a>

The following command deletes an object named test\.txt from a bucket named `DOC-EXAMPLE-BUCKET1`\. To remove a specific version of an object, you must be the bucket owner and you must use the version Id subresource\.

```
aws s3api delete-object --bucket DOC-EXAMPLE-BUCKET1 --key test.txt --version-id versionID
```

For more information about `delete-object` see [https://docs.aws.amazon.com/cli/latest/reference/s3api/delete-object.html](https://docs.aws.amazon.com/cli/latest/reference/s3api/delete-object.html) in the *AWS CLI Command Reference*\.

For more information about deleting object versions, see the following topics:
+ [Working with delete markers](DeleteMarker.md)
+ [Removing delete markers to make an older version current](ManagingDelMarkers.md#RemDelMarker)
+ [Deleting an object from an MFA delete\-enabled bucket](UsingMFADelete.md)