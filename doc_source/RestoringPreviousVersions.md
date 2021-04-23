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