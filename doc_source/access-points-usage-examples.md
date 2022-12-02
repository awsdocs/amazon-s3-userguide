# Using access points with compatible Amazon S3 operations<a name="access-points-usage-examples"></a>

The following examples demonstrate how to use access points with compatible operations in Amazon S3\.

**Topics**
+ [Access point compatibility with AWS services](#access-points-service-api-support)
+ [Access point compatibility with S3 operations](#access-points-operations-support)
+ [Request an object through an access point](#get-object-ap)
+ [Upload an object through an access point alias](#put-object-ap)
+ [Delete an object through an access point](#delete-object-ap)
+ [List objects through an access point alias](#list-object-ap)
+ [Add a tag set to an object through an access point](#add-tag-set-ap)
+ [Grant access permissions through an access point using an ACL](#acl-permissions-ap)

## Access point compatibility with AWS services<a name="access-points-service-api-support"></a>

Amazon S3 access points aliases allow any application that requires an S3 bucket name to easily use an access point\. You can use S3 access point aliases anywhere you use S3 bucket names to access data in S3\.

## Access point compatibility with S3 operations<a name="access-points-operations-support"></a>

You can use access points to access a bucket using the following subset of Amazon S3 APIs\. All the operations listed below can accept either access point ARNs or access point aliases:

**S3 operations**
+ `[AbortMultipartUpload](https://docs.aws.amazon.com/AmazonS3/latest/API/API_AbortMultipartUpload.html)`
+ `[CompleteMultipartUpload](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CompleteMultipartUpload.html)`
+ `[CopyObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html)` \(same\-region copies only\)
+ `[CreateMultipartUpload](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CreateMultipartUpload.html)`
+ `[DeleteObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteObject.html)`
+ `[DeleteObjectTagging](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteObjectTagging.html)`
+ `[GetBucketAcl](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketAcl.html)`
+ `[GetBucketCors](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketCors.html)`
+ `[GetBucketLocation](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketLocation.html)`
+ `[GetBucketNotificationConfiguration](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketNotificationConfiguration.html)`
+ `[GetBucketPolicy](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketPolicy.html)`
+ `[GetObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html)`
+ `[GetObjectAcl](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectAcl.html)`
+ `[GetObjectLegalHold](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectLegalHold.html)`
+ `[GetObjectRetention](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectRetention.html)`
+ `[GetObjectTagging](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectTagging.html)`
+ `[HeadBucket](https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadBucket.html)`
+ `[HeadObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadObject.html)`
+ `[ListMultipartUploads](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListMultipartUploads.html)`
+ `[ListObjects](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListObjects.html)`
+ `[ListObjectsV2](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListObjectsV2.html)`
+ `[ListParts](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListParts.html)`
+ `[Presign](https://docs.aws.amazon.com/AmazonS3/latest/API/sigv4-query-string-auth.html)`
+ `[PutObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html)`
+ `[PutObjectLegalHold](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectLegalHold.html)`
+ `[PutObjectRetention](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectRetention.html)`
+ `[PutObjectAcl](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectAcl.html)`
+ `[PutObjectTagging](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectTagging.html)`
+ `[RestoreObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_RestoreObject.html)`
+ `[UploadPart](https://docs.aws.amazon.com/AmazonS3/latest/API/API_UploadPart.html)`
+ `[UploadPartCopy](https://docs.aws.amazon.com/AmazonS3/latest/API/API_UploadPartCopy.html)` \(same\-region copies only\)

## Request an object through an access point<a name="get-object-ap"></a>

The following example requests the object `my-image.jpg` through the access point `prod` owned by account ID `123456789012` in Region `us-west-2`, and saves the downloaded file as `download.jpg`\.

------
#### [ AWS CLI ]

```
aws s3api get-object --key my-image.jpg --bucket arn:aws:s3:us-west-2:123456789012:accesspoint/prod download.jpg
```

------

## Upload an object through an access point alias<a name="put-object-ap"></a>

The following example uploads the object `my-image.jpg` through the access point alias `my-access-point-hrzrlukc5m36ft7okagglf3gmwluquse1b-s3alias` owned by account ID `123456789012` in Region `us-west-2`\.

------
#### [ AWS CLI ]

```
aws s3api put-object --bucket my-access-point-hrzrlukc5m36ft7okagglf3gmwluquse1b-s3alias --key my-image.jpg --body my-image.jpg
```

------

## Delete an object through an access point<a name="delete-object-ap"></a>

The following example deletes the object `my-image.jpg` through the access point `prod` owned by account ID `123456789012` in Region `us-west-2`\.

------
#### [ AWS CLI ]

```
aws s3api delete-object --bucket arn:aws:s3:us-west-2:123456789012:accesspoint/prod --key my-image.jpg
```

------

## List objects through an access point alias<a name="list-object-ap"></a>

The following example lists objects through the access point alias `my-access-point-hrzrlukc5m36ft7okagglf3gmwluquse1b-s3alias` owned by account ID `123456789012` in Region `us-west-2`\.

------
#### [ AWS CLI ]

```
aws s3api list-objects-v2 --bucket my-access-point-hrzrlukc5m36ft7okagglf3gmwluquse1b-s3alias
```

------

## Add a tag set to an object through an access point<a name="add-tag-set-ap"></a>

The following example adds a tag set to the existing object `my-image.jpg` through the access point `prod` owned by account ID `123456789012` in Region `us-west-2`\.

------
#### [ AWS CLI ]

```
aws s3api put-object-tagging --bucket arn:aws:s3:us-west-2:123456789012:accesspoint/prod --key my-image.jpg --tagging TagSet=[{Key="finance",Value="true"}]
```

------

## Grant access permissions through an access point using an ACL<a name="acl-permissions-ap"></a>

The following example applies an ACL to an existing object `my-image.jpg` through the access point `prod` owned by account ID `123456789012` in Region `us-west-2`\.

------
#### [ AWS CLI ]

```
aws s3api put-object-acl --bucket arn:aws:s3:us-west-2:123456789012:accesspoint/prod --key my-image.jpg --acl private
```

------