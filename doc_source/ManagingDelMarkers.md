# Managing delete markers<a name="ManagingDelMarkers"></a>

## Configuring lifecycle to clean up expired delete markers automatically<a name="LifecycleDelMarker"></a>

An expired object delete marker is one where all object versions are deleted and only a single delete marker remains\. If the lifecycle policy is set to delete current versions, or the `ExpiredObjectDeleteMarker` action is explicitly set, Amazon S3 removes the expired object’s delete marker\. For an example, see [Example 7: Removing expired object delete markers](lifecycle-configuration-examples.md#lifecycle-config-conceptual-ex7)\. 

## Removing delete markers to make an older version current<a name="RemDelMarker"></a>

When you delete an object in a versioning\-enabled bucket, all versions remain in the bucket, and Amazon S3 creates a delete marker for the object\. To undelete the object, you must delete this delete marker\. For more information about versioning and delete markers, see [Using versioning in S3 buckets](Versioning.md)\.

To delete a delete marker permanently, you must include its version ID in a `DeleteObject versionId` request\. The following figure shows how a `DeleteObject versionId` request permanently removes a delete marker\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_DELETE_deleteMarkerVersioned.png)

The effect of removing the delete marker is that a simple `GET` request will now retrieve the current version ID \(121212\) of the object\. 

**Note**  
If you use a `DeleteObject` request where the current version is a delete marker \(without specifying the version ID of the delete marker\), Amazon S3 does not delete the delete marker, but instead `PUTs` another delete marker\.

To delete a delete marker with a `NULL` version ID, you must pass the `NULL` as the version ID in the `DeleteObject` request\. The following figure shows how a simple `DeleteObject` request made without a version ID where the current version is a delete marker, removes nothing, but instead adds an additional delete marker with a unique version ID \(7498372\)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_DELETE_deleteMarker.png)

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

## Using the AWS SDKs<a name="remove-delete-marker-examples-sdk"></a>

TFor information about using other AWS SDKs, see the [AWSDeveloper Center](https://aws.amazon.com/code/)\.

------
#### [ Python ]

For instructions on how to create and test a working sample, see [Using the AWS SDK for Python \(Boto\)](UsingTheBotoAPI.md)\. 

The following Python code example demonstrates how to remove a delete marker from an object and thus makes the most recent non\-current version, the current version of the object\.

```
def revive_object(bucket, object_key):
    """
    Revives a versioned object that was deleted by removing the object's active
    delete marker.
    A versioned object presents as deleted when its latest version is a delete marker.
    By removing the delete marker, we make the previous version the latest version
    and the object then presents as *not* deleted.

    Usage is shown in the usage_demo_single_object function at the end of this module.

    :param bucket: The bucket that contains the object.
    :param object_key: The object to revive.
    """
    # Get the latest version for the object.
    response = s3.meta.client.list_object_versions(
        Bucket=bucket.name, Prefix=object_key, MaxKeys=1)

    if 'DeleteMarkers' in response:
        latest_version = response['DeleteMarkers'][0]
        if latest_version['IsLatest']:
            logger.info("Object %s was indeed deleted on %s. Let's revive it.",
                        object_key, latest_version['LastModified'])
            obj = bucket.Object(object_key)
            obj.Version(latest_version['VersionId']).delete()
            logger.info("Revived %s, active version is now %s  with body '%s'",
                        object_key, obj.version_id, obj.get()['Body'].read())
        else:
            logger.warning("Delete marker is not the latest version for %s!",
                           object_key)
    elif 'Versions' in response:
        logger.warning("Got an active version for %s, nothing to do.", object_key)
    else:
        logger.error("Couldn't get any version info for %s.", object_key)
```

------