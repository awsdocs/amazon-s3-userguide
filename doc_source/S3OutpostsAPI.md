# Amazon S3 on Outposts API operations<a name="S3OutpostsAPI"></a>

This topic lists the Amazon S3, Amazon S3 Control, and Amazon S3 on Outposts API operations that you can use with Amazon S3 on Outposts\.

**Topics**
+ [Amazon S3 API operations for managing objects](#S3OutpostsAPIsObject)
+ [Amazon S3 Control API operations for managing buckets](#S3OutpostsAPIsBucket)
+ [S3 on Outposts API operations for managing Outposts](#S3OutpostsAPIs)

## Amazon S3 API operations for managing objects<a name="S3OutpostsAPIsObject"></a>

S3 on Outposts is designed to use the same object API operations as Amazon S3\. You must use access points to access any object in an Outpost bucket\. When you use an object API operation with S3 on Outposts, you provide either the Outposts access point Amazon Resource Name \(ARN\) or the access point alias\. For more information about access point aliases, see [Using a bucket\-style alias for your S3 on Outposts bucket access point](s3-outposts-access-points-alias.md)\.

Amazon S3 on Outposts supports the following Amazon S3 API operations:
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_AbortMultipartUpload.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_AbortMultipartUpload.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_CompleteMultipartUpload.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CompleteMultipartUpload.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_CreateMultipartUpload.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CreateMultipartUpload.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteObject.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteObject.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteObjects.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteObjects.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteObjectTagging.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteObjectTagging.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectTagging.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectTagging.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadBucket.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadBucket.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadObject.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadObject.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListMultipartUploads.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListMultipartUploads.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListObjects.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListObjects.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListObjectsV2.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListObjectsV2.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListParts.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListParts.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectTagging.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectTagging.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_UploadPart.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_UploadPart.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_UploadPartCopy.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_UploadPartCopy.html)

## Amazon S3 Control API operations for managing buckets<a name="S3OutpostsAPIsBucket"></a>

S3 on Outposts supports the following Amazon S3 Control API operations for working with buckets\.
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_CreateAccessPoint.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_CreateAccessPoint.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_CreateBucket.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_CreateBucket.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeleteAccessPoint.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeleteAccessPoint.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeleteAccessPointPolicy.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeleteAccessPointPolicy.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeleteBucket.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeleteBucket.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeleteBucketLifecycleConfiguration.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeleteBucketLifecycleConfiguration.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeleteBucketPolicy.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeleteBucketPolicy.html)
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeleteBucketReplication.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeleteBucketReplication.html) 
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeleteBucketTagging.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeleteBucketTagging.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetAccessPoint.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetAccessPoint.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetAccessPointPolicy.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetAccessPointPolicy.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetBucket.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetBucket.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetBucketLifecycleConfiguration.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetBucketLifecycleConfiguration.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetBucketPolicy.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetBucketPolicy.html)
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetBucketReplication.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetBucketReplication.html) 
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetBucketTagging.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetBucketTagging.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_ListAccessPoints.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_ListAccessPoints.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_ListRegionalBuckets.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_ListRegionalBuckets.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutAccessPointPolicy.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutAccessPointPolicy.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutBucketLifecycleConfiguration.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutBucketLifecycleConfiguration.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutBucketPolicy.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutBucketPolicy.html)
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutBucketReplication.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutBucketReplication.html) 
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutBucketTagging.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutBucketTagging.html)

## S3 on Outposts API operations for managing Outposts<a name="S3OutpostsAPIs"></a>

S3 on Outposts supports the following Amazon S3 on Outposts API operations for managing endpoints\.
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_s3outposts_CreateEndpoint.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_s3outposts_CreateEndpoint.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_s3outposts_DeleteEndpoint.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_s3outposts_DeleteEndpoint.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_s3outposts_ListEndpoints.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_s3outposts_ListEndpoints.html)
+ [ListOutpostsWithS3](https://docs.aws.amazon.com/AmazonS3/latest/API/API_s3outposts_ListOutpostsWithS3.html)
+ [ListOutpostsWithS3](https://docs.aws.amazon.com/AmazonS3/latest/API/API_s3outposts_ListOutpostsWithS3.html)
+ [https://docs.aws.amazon.com/AmazonS3/latest/API/API_s3outposts_ListSharedEndpoints.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_s3outposts_ListSharedEndpoints.html)