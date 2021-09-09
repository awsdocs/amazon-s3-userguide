# Multi\-Region Access Point supported operations<a name="MrapOperations"></a>

 You can use Multi\-Region Access Point to access buckets using the following subset of Amazon S3 APIs: 
+ `[AbortMultipartUpload](https://docs.aws.amazon.com/AmazonS3/latest/API/API_AbortMultipartUpload.html)`
+ `[CompleteMultipartUpload](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CompleteMultipartUpload.html)`
+ `[CreateMultipartUpload](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CreateMultipartUpload.html)`
+ `[DeleteObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteObject.html)`
+ `[DeleteObjectTagging](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteObjectTagging.html)`
+ `[GetObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html)`
+ `[GetObjectAcl](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectAcl.html)`
+ `[GetObjectLegalHold](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectLegalHold.html)`
+ `[GetObjectRetention](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectRetention.html)`
+ `[GetObjectTagging](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectTagging.html)`
+ `[HeadObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadObject.html)`
+ `[ListMultipartUploads](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListMultipartUploads.html)`
+ `[ListObjectsV2](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListObjectsV2.html)`
+ `[ListParts](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListParts.html)`
+ `[PutObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html)`
+ `[PutObjectAcl](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectAcl.html)`
+ `[PutObjectLegalHold](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectLegalHold.html)`
+ `[PutObjectRetention](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectRetention.html)`
+ `[PutObjectTagging](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectTagging.html)`
+ `[RestoreObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_RestoreObject.html)`
+ `[UploadPart](https://docs.aws.amazon.com/AmazonS3/latest/API/API_UploadPart.html)`

**Note**  
Multi\-Region Access Points do not support the [CopyObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html) API operation\. Instead you will need to perform `CopyObject` actions directly between buckets\.