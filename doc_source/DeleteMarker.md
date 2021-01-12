--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Working with delete markers<a name="DeleteMarker"></a>

A delete marker in Amazon S3 is a placeholder \(or marker\) for a versioned object that was named in a simple `DELETE` request\. Because the object was in a versioning\-enabled bucket, the object was not deleted\. But the delete marker makes Amazon S3 behave as if it had been deleted\. 

A delete marker has a key name \(or key\) and version ID like any other object\. However, a delete marker differs from other objects in the following ways:
+ It does not have data associated with it\.
+ It is not associated with an access control list \(ACL\) value\.
+ It does not retrieve anything from a `GET` request because it has no data; you get a 404 error\.
+ The only operation that you can use on a delete marker is an Amazon S3 API `DELETE` call\. To do this, you must make the `DELETE` request using an AWS Identity and Access Management \(IAM\) user or role with the appropriate permissions\.

Delete markers accrue a nominal charge for storage in Amazon S3\. The storage size of a delete marker is equal to the size of the key name of the delete marker\. A key name is a sequence of Unicode characters\. The UTF\-8 encoding adds 1–4 bytes of storage to your bucket for each character in the name\. 

For more information about key names, see [Creating object key names](object-keys.md)\. For information about deleting a delete marker, see [Removing delete markers](#RemDelMarker)\.  

Only Amazon S3 can create a delete marker, and it does so whenever you send a `DELETE Object` request on an object in a versioning\-enabled or suspended bucket\. The object named in the `DELETE` request is not actually deleted\. Instead, the delete marker becomes the current version of the object\. \(The object's key name \(or key\) becomes the key of the delete marker\.\) If you try to get an object and its current version is a delete marker, Amazon S3 responds with the following:
+ A 404 \(Object not found\) error
+ A response header, `x-amz-delete-marker: true`

The response header tells you that the object accessed was a delete marker\. This response header never returns `false`\. If the value is `false`, Amazon S3 does not include this response header in the response\.

The following figure shows how a simple `GET` on an object, whose current version is a delete marker, returns a 404 No Object Found error\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_DELETE_NoObjectFound.png)

The only way to list delete markers \(and other versions of an object\) is by using the `versions` subresource in a `GET Bucket versions` request\. A simple `GET` does not retrieve delete marker objects\. The following figure shows that a `GET Bucket` request does not return objects whose current version is a delete marker\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_GETBucketwithDeleteMarkers.png)

## Removing delete markers<a name="RemDelMarker"></a>

When you delete an object in a versioning\-enabled bucket, all versions remain in the bucket, and Amazon S3 creates a delete marker for the object\. To undelete the object, you must delete this delete marker\. For more information about versioning and delete markers, see [Using versioning in S3 buckets](Versioning.md)\.

To delete a delete marker, the delete marker must have a version ID, and you must specify that ID in a `DELETE Object versionId` request\. If you use a `DELETE` request to delete a delete marker \(without specifying the version ID of the delete marker\), Amazon S3 does not delete the delete marker, but instead, inserts another delete marker\.

The following figure shows how a simple `DELETE` on a delete marker removes nothing, but adds a new delete marker to a bucket\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_DELETE_deleteMarker.png)

In a versioning\-enabled bucket, this new delete marker would have a unique version ID\. So, it's possible to have multiple delete markers of the same object in one bucket\. 

If the current object version is the only object version and it is also a delete marker \(also referred as an *expired object delete marker*, where all object versions are deleted and you only have a delete marker remaining\), Amazon S3 removes the expired object delete marker\. You can also use the expiration action to direct Amazon S3 to remove any expired object delete markers\. For an example, see [Example 7: Removing expired object delete markers](lifecycle-configuration-examples.md#lifecycle-config-conceptual-ex7)\. 

To permanently delete a delete marker, you must include its version ID in a `DELETE Object versionId` request\. The following figure shows how a `DELETE Object versionId` request permanently removes a delete marker\. Only the owner of a bucket can permanently remove a delete marker\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_DELETE_deleteMarkerVersioned.png)

The effect of removing the delete marker is that a simple `GET` request will now retrieve the current version \(121212\) of the object\.

### To remove delete markers<a name="remove-delete-markers"></a>

You can use the console and REST API to remove delete markers\.

#### Using the S3 console<a name="undelete-objects"></a>

Use the following steps in the console to recover deleted objects that are not folders from your S3 bucket, including objects that are within those folders\. 

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want\.

1. To see a list of the **versions** of the objects in the bucket, choose the **List versions** switch\. You'll be able to see the delete markers for deleted objects\. 

   

1. To undelete an object, you must delete the delete marker\. Select the check box next to the **delete marker** of the object to recover, and then choose **Delete**\.

1. Confirm the deletion on the **Delete objects** page\.

   1. For **Pemanently delete objects?** enter **permanently delete**\.

   1. Choose **Delete objects**\.

**Note**  
You can't use the Amazon S3 console to undelete folders\. You must use the AWS CLI or SDK\. For examples, see [ How can I retrieve an Amazon S3 object that was deleted in a versioning\-enabled bucket?](http://aws.amazon.com/premiumsupport/knowledge-center/s3-undelete-configuration/) 

#### Using the REST API<a name="delete-marker-rest-api"></a>

**To permanently remove a delete marker**

1. Set `versionId` to the ID of the version to the delete marker you want to remove\.

1. Send a `DELETE Object versionId` request\.

**Example — Removing a delete marker**  
The following example removes the delete marker for `photo.gif` version 4857693\.  

```
1. DELETE /photo.gif?versionId=4857693 HTTP/1.1
2. Host: bucket.s3.amazonaws.com
3. Date: Wed, 28 Oct 2009 22:32:00 GMT
4. Authorization: AWS AKIAIOSFODNN7EXAMPLE:0RQf4/cRonhpaBX5sCYVf1bNRuU=
```

When you delete a delete marker, Amazon S3 includes in the response:

```
1. 204 NoContent 
2. x-amz-version-id: versionID 
3. x-amz-delete-marker: true
```