# Adding objects to versioning\-enabled buckets<a name="AddingObjectstoVersioningEnabledBuckets"></a>

After you enable versioning on a bucket, Amazon S3 automatically adds a unique version ID to every object stored \(using `PUT`, `POST`, or `COPY`\) in the bucket\. 

The following figure shows that Amazon S3 adds a unique version ID to an object when it is added to a versioning\-enabled bucket\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_PUT_versionEnabled.png)

**Note**  
Normal Amazon S3 rates apply for every version of an object stored and transferred\. Each version of an object is the entire object; it is not just a diff from the previous version\. Thus, if you have three versions of an object stored, you are charged for three objects\.   
The version ID values that Amazon S3 assigns are URL safe \(can be included as part of a URI\)\.

You can add object versions to a versioning\-enabled bucket using the console, AWS SDKs, and REST API\.

## Using the console<a name="add-obj-versioning-enabled-bucket-console"></a>

For instructions, see [Uploading objects](upload-objects.md)\. 

## Using the AWS SDKs<a name="add-obj-versioning-enabled-bucket-sdk"></a>

For examples of uploading objects using the AWS SDKs for Java, \.NET, and PHP, see [Uploading objects](upload-objects.md)\. The examples for uploading objects in nonversioned and versioning\-enabled buckets are the same, although in the case of versioning\-enabled buckets, Amazon S3 assigns a version number\. Otherwise, the version number is null\. 

For information about using other AWS SDKs, see the [AWS Developer Center](https://aws.amazon.com/code/)\. 

## Using the REST API<a name="add-obj-versioning-enabled-bucket-rest"></a>

**To add objects to versioning\-enabled buckets**

1. Enable versioning on a bucket using a `PUT Bucket versioning` request\.

   For more information, see [PutBucketVersioning](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTVersioningStatus.html) in the *Amazon Simple Storage Service API Reference*\.

1. Send a `PUT`, `POST`, or `COPY` request to store an object in the bucket\.

When you add an object to a versioning\-enabled bucket, Amazon S3 returns the version ID of the object in the `x-amz-version-id` response header, as shown in the following example\.

```
1. x-amz-version-id: 3/L4kqtJlcpXroDTDmJ+rmSpXd3dIbrHY
```