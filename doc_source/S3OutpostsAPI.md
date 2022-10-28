# Amazon S3 on Outposts API operations<a name="S3OutpostsAPI"></a>

This topic lists the Amazon S3, Amazon S3 Control, and Amazon S3 on Outposts API operations that you can use with Amazon S3 on Outposts\.

**Topics**
+ [Amazon S3 API operations for managing objects](#S3OutpostsAPIsObject)
+ [Amazon S3 Control API operations for managing buckets](#S3OutpostsAPIsBucket)
+ [S3 on Outposts API operations for managing endpoints](#S3OutpostsAPIs)

## Amazon S3 API operations for managing objects<a name="S3OutpostsAPIsObject"></a>

S3 on Outposts is designed to use the same object API operations as Amazon S3\. You must use access points to access any object in an Outpost bucket\. When you use an object API operation with S3 on Outposts, you provide either the Outposts access point Amazon Resource Name \(ARN\) or the access point alias\. For more information about access point aliases, see [Using a bucket\-style alias for your S3 on Outposts bucket access point](s3-outposts-access-points-alias.md)\.

Amazon S3 on Outposts supports the following Amazon S3 API operations:
+ [AbortMultipartUpload](https://docs.aws.amazon.com/AmazonS3/latest/API/API_AbortMultipartUpload.html)
+ [CompleteMultipartUpload](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CompleteMultipartUpload.html)
+ [CopyObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html)
+ [CreateMultipartUpload](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CreateMultipartUpload.html)
+ [DeleteObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteObject.html)
+ [DeleteObjects](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteObjects.html)
+ [DeleteObjectTagging](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteObjectTagging.html)
+ [GetObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html)
+ [GetObjectTagging](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectTagging.html)
+ [HeadBucket](https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadBucket.html)
+ [HeadObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadObject.html)
+ [ListMultipartUploads](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListMultipartUploads.html)
+ [ListObjects](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListObjects.html)
+ [ListObjectsV2](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListObjectsV2.html)
+ [ListParts](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListParts.html)
+ [PutObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html)
+ [PutObjectTagging](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectTagging.html)
+ [UploadPart](https://docs.aws.amazon.com/AmazonS3/latest/API/API_UploadPart.html)
+ [UploadPartCopy](https://docs.aws.amazon.com/AmazonS3/latest/API/API_UploadPartCopy.html)

## Amazon S3 Control API operations for managing buckets<a name="S3OutpostsAPIsBucket"></a>

S3 on Outposts supports the following Amazon S3 Control API operations for working with buckets\.
+ [CreateAccessPoint](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_CreateAccessPoint.html)
+ [CreateBucket](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_CreateBucket.html)
+ [DeleteAccessPoint](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeleteAccessPoint.html)
+ [DeleteAccessPointPolicy](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeleteAccessPointPolicy.html)
+ [DeleteBucket](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeleteBucket.html)
+ [DeleteBucketLifecycleConfiguration](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeleteBucketLifecycleConfiguration.html)
+ [DeleteBucketPolicy](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeleteBucketPolicy.html)
+ [DeleteBucketTagging](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeleteBucketTagging.html)
+ [GetAccessPoint](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetAccessPoint.html)
+ [GetAccessPointPolicy](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetAccessPointPolicy.html)
+ [GetBucket](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetBucket.html)
+ [GetBucketLifecycleConfiguration](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetBucketLifecycleConfiguration.html)
+ [GetBucketPolicy](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetBucketPolicy.html)
+ [GetBucketTagging](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetBucketTagging.html)
+ [ListAccessPoints](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_ListAccessPoints.html)
+ [ListRegionalBuckets](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_ListRegionalBuckets.html)
+ [PutAccessPointPolicy](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutAccessPointPolicy.html)
+ [PutBucketLifecycleConfiguration](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutBucketLifecycleConfiguration.html)
+ [PutBucketPolicy](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutBucketPolicy.html)
+ [PutBucketTagging](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutBucketTagging.html)

## S3 on Outposts API operations for managing endpoints<a name="S3OutpostsAPIs"></a>

S3 on Outposts supports the following Amazon S3 on Outposts API operations for managing endpoints\.
+ [CreateEndpoint](https://docs.aws.amazon.com/AmazonS3/latest/API/API_s3outposts_CreateEndpoint.html)
+ [DeleteEndpoint](https://docs.aws.amazon.com/AmazonS3/latest/API/API_s3outposts_DeleteEndpoint.html)
+ [ListEndpoints](https://docs.aws.amazon.com/AmazonS3/latest/API/API_s3outposts_ListEndpoints.html)
+ [ListSharedEndpoints](https://docs.aws.amazon.com/AmazonS3/latest/API/API_s3outposts_ListSharedEndpoints.html)