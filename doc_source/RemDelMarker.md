--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Removing delete markers<a name="RemDelMarker"></a>

When you delete an object in a versioning\-enabled bucket, all versions remain in the bucket, and Amazon S3 creates a delete marker for the object\. To undelete the object, you must delete this delete marker\. For more information about versioning and delete markers, see [Using versioning in S3 buckets](Versioning.md)\.

If you want to delete a delete marker, it must have a version ID, and you must specify that ID in a `DELETE Object versionId` request\. If you use a `DELETE` request to delete a delete marker \(without specifying the version ID of the delete marker\), Amazon S3 does not delete the delete marker, but instead, inserts another delete marker\.

The following figure shows how a simple `DELETE` on a delete marker removes nothing, but adds a new delete marker to a bucket\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_DELETE_deleteMarker.png)

In a versioning\-enabled bucket, this new delete marker would have a unique version ID\. So, it's possible to have multiple delete markers of the same object in one bucket\. 

If the current object version is the only object version, and it is also a delete marker \(known as an *expired object delete marker*, where all object versions are deleted and only a delete marker remains\), Amazon S3 removes the expired object delete marker\. You can also use the expiration action to direct Amazon S3 to remove any expired object delete markers\. For an example, see [Example 7: Removing expired object delete markers](lifecycle-configuration-examples.md#lifecycle-config-conceptual-ex7)\. 

To delete a delete marker permanently, you must include its version ID in a `DELETE Object versionId` request\. The following figure shows how a `DELETE Object versionId` request permanently removes a delete marker\. Only the owner of a bucket can permanently remove a delete marker\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_DELETE_deleteMarkerVersioned.png)

The effect of removing the delete marker is that a simple `GET` request will now retrieve the current version \(121212\) of the object\.

## Using the S3 console<a name="undelete-objects"></a>

Use the following steps to recover deleted objects that are not folders from your S3 bucket, including objects that are within those folders\. 

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want\.

1. To see a list of the **versions** of the objects in the bucket, choose the **List versions** switch\. You'll be able to see the delete markers for deleted objects\. 

   

1. To undelete an object, you must delete the delete marker\. Select the check box next to the **delete marker** of the object to recover, and then choose **Delete**\.

1. Confirm the deletion on the **Delete objects** page\.

   1. For **Pemanently delete objects?** enter **permanently delete**\.

   1. Choose **Delete objects**\.

**Note**  
You can't use the Amazon S3 console to undelete folders\. You must use the AWS CLI or SDK\. For examples, see [ How can I retrieve an Amazon S3 object that was deleted in a versioning\-enabled bucket?](http://aws.amazon.com/premiumsupport/knowledge-center/s3-undelete-configuration/) in the AWS Knowledge Center\.

## Using the REST API<a name="delete-marker-rest-api"></a>

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

When you delete a delete marker, Amazon S3 includes the following in the response\.

```
1. 204 NoContent 
2. x-amz-version-id: versionID 
3. x-amz-delete-marker: true
```