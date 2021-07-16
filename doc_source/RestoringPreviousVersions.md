# Restoring previous versions<a name="RestoringPreviousVersions"></a>

You can use versioning to retrieve previous versions of an object\. There are two approaches to doing so:
+ Copy a previous version of the object into the same bucket\.

  The copied object becomes the current version of that object and all object versions are preserved\.
+ Permanently delete the current version of the object\.

  When you delete the current object version, you, in effect, turn the previous version into the current version of that object\.

Because all object versions are preserved, you can make any earlier version the current version by copying a specific version of the object into the same bucket\. In the following figure, the source object \(ID = 111111\) is copied into the same bucket\. Amazon S3 supplies a new ID \(88778877\) and it becomes the current version of the object\. So, the bucket has both the original object version \(111111\) and its copy \(88778877\)\. For more information about getting a previous version and then uploading it to make it the current version, see [Retrieving object versions from a versioning\-enabled bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/RetrievingObjectVersions.html) and [Uploading objects](https://docs.aws.amazon.com/AmazonS3/latest/userguide/upload-objects.html)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_COPY2.png)

A subsequent `GET` retrieves version 88778877\.

The following figure shows how deleting the current version \(121212\) of an object leaves the previous version \(111111\) as the current object\. For more information about deleting an object, see [Deleting a single object](https://docs.aws.amazon.com/AmazonS3/latest/userguide/delete-objects.html)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_COPY_delete2.png)

A subsequent `GET` retrieves version 111111\.



## To restore previous object versions<a name="restoring-obj-version-version-enabled-bucket-examples"></a>

### Using the AWS SDKs<a name="restoring-obj-version-version-enabled-bucket-sdks"></a>

For information about using other AWS SDKs, see the [AWS Developer Center](https://aws.amazon.com/code/)\. 

------
#### [ Python ]

For instructions on how to create and test a working sample, see [Using the AWS SDK for Python \(Boto\)](UsingTheBotoAPI.md)\. 

The following Python code example restores a versioned object's previous version by deleting all versions that occurred after the specified rollback version\.

```
									def rollback_object(bucket, object_key, version_id):
    """
    Rolls back an object to an earlier version by deleting all versions that
    occurred after the specified rollback version.

    Usage is shown in the usage_demo_single_object function at the end of this module.

    :param bucket: The bucket that holds the object to roll back.
    :param object_key: The object to roll back.
    :param version_id: The version ID to roll back to.
    """
    # Versions must be sorted by last_modified date because delete markers are
    # at the end of the list even when they are interspersed in time.
    versions = sorted(bucket.object_versions.filter(Prefix=object_key),
                      key=attrgetter('last_modified'), reverse=True)

    logger.debug(
        "Got versions:\n%s",
        '\n'.join([f"\t{version.version_id}, last modified {version.last_modified}"
                   for version in versions]))

    if version_id in [ver.version_id for ver in versions]:
        print(f"Rolling back to version {version_id}")
        for version in versions:
            if version.version_id != version_id:
                version.delete()
                print(f"Deleted version {version.version_id}")
            else:
                break

        print(f"Active version is now {bucket.Object(object_key).version_id}")
    else:
        raise KeyError(f"{version_id} was not found in the list of versions for "
                       f"{object_key}.")
```

------