--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Retrieving object versions from a versioning\-enabled bucket<a name="RetrievingObjectVersions"></a>

Versioning in Amazon S3 is a way of keeping multiple variants of an object in the same bucket\. A simple `GET` request retrieves the current version of an object\. The following figure shows how `GET` returns the current version of the object, `photo.gif`\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_GET_NoVersionID.png)

To retrieve a specific version, you have to specify its version ID\. The following figure shows that a `GET versionId` request retrieves the specified version of the object \(not necessarily the current one\)\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_GET_Versioned.png)

**Topics**
+ [Restoring previous versions](#RestoringPreviousVersions)
+ [Retrieving object versions](#retrieving-object-versions)
+ [Retrieving the metadata of an object version](#RetMetaOfObjVersion)

## Restoring previous versions<a name="RestoringPreviousVersions"></a>

One of the value propositions of versioning is the ability to retrieve previous versions of an object\. There are two approaches to doing so:
+ Copy a previous version of the object into the same bucket\.

  The copied object becomes the current version of that object and all object versions are preserved\.
+ Permanently delete the current version of the object\.

  When you delete the current object version, you, in effect, turn the previous version into the current version of that object\.

Because all object versions are preserved, you can make any earlier version the current version by copying a specific version of the object into the same bucket\. In the following figure, the source object \(ID = 111111\) is copied into the same bucket\. Amazon S3 supplies a new ID \(88778877\) and it becomes the current version of the object\. So, the bucket has both the original object version \(111111\) and its copy \(88778877\)\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_COPY2.png)

A subsequent `GET` retrieves version 88778877\.

The following figure shows how deleting the current version \(121212\) of an object leaves the previous version \(111111\) as the current object\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_COPY_delete2.png)

A subsequent `GET` retrieves version 111111\.

## Retrieving object versions<a name="retrieving-object-versions"></a>

You can retrieve object versions in Amazon S3 using the console, AWS SDKs, or REST API\.

### Using the S3 console<a name="retrieving-object-versions"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that contains the object\.

1. In the **Objects** list, choose the name of the object\.

1. Choose **Versions**\.

   Amazon S3 shows all the versions for the object\.

1. Select the check box next to the **Version ID** for the versions that you want to retrieve\.

1. Choose **Actions**, choose **Download**, and save the object\.

You also can view, download, and delete object versions in the object overview panel\. For more information, see [Viewing an object overview in the Amazon S3 console](view-object-overview.md)\.

**Important**  
You can undelete an object only if it was deleted as the latest \(current\) version\. You can't undelete a previous version of an object that was deleted\. For more information, see [Using versioning in S3 buckets](Versioning.md)\.

### Using the AWS SDKs<a name="retrieve-obj-version-sdks"></a>

For examples of uploading objects using AWS SDKs for Java, \.NET, and PHP, see [Downloading an objectDownloading an object using the Amazon S3 console](download-objects.md)\. The examples for uploading objects in nonversioned and versioning\-enabled buckets are the same\. However, for versioning\-enabled buckets, Amazon S3 assigns a version number\. Otherwise, the version number is null\.

### Using the REST API<a name="retrieve-obj-version-rest"></a>

**To retrieve a specific object version**

1. Set `versionId` to the ID of the version of the object that you want to retrieve\.

1. Send a `GET Object versionId` request\.

**Example — Retrieving a versioned object**  
The following request retrieves version `L4kqtJlcpXroDTDmpUMLUo` of `my-image.jpg`\.  

```
1. GET /my-image.jpg?versionId=L4kqtJlcpXroDTDmpUMLUo HTTP/1.1
2. Host: bucket.s3.amazonaws.com
3. Date: Wed, 28 Oct 2009 22:32:00 GMT
4. Authorization: AWS AKIAIOSFODNN7EXAMPLE:0RQf4/cRonhpaBX5sCYVf1bNRuU=
```

## Retrieving the metadata of an object version<a name="RetMetaOfObjVersion"></a>

If you only want to retrieve the metadata of an object \(and not its content\), you use the `HEAD` operation\. By default, you get the metadata of the most recent version\. To retrieve the metadata of a specific object version, you specify its version ID\.

**To retrieve the metadata of an object version**

1. Set `versionId` to the ID of the version of the object whose metadata you want to retrieve\.

1. Send a `HEAD Object versionId` request\.

**Example — Retrieving the metadata of a versioned object**  
The following request retrieves the metadata of version 3HL4kqCxf3vjVBH40Nrjfkd of `my-image.jpg`\.  

```
1. HEAD /my-image.jpg?versionId=3HL4kqCxf3vjVBH40Nrjfkd HTTP/1.1
2. Host: bucket.s3.amazonaws.com
3. Date: Wed, 28 Oct 2009 22:32:00 GMT
4. Authorization: AWS AKIAIOSFODNN7EXAMPLE:0RQf4/cRonhpaBX5sCYVf1bNRuU=
```

The following shows a sample response\.

```
 1. HTTP/1.1 200 OK
 2. x-amz-id-2: ef8yU9AS1ed4OpIszj7UDNEHGran
 3. x-amz-request-id: 318BC8BC143432E5
 4. x-amz-version-id: 3HL4kqtJlcpXroDTDmjVBH40Nrjfkd
 5. Date: Wed, 28 Oct 2009 22:32:00 GMT
 6. Last-Modified: Sun, 1 Jan 2006 12:00:00 GMT
 7. ETag: "fba9dede5f27731c9771645a39863328"
 8. Content-Length: 434234
 9. Content-Type: text/plain
10. Connection: close
11. Server: AmazonS3
```