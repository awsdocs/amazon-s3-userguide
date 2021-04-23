# Viewing settings for an S3 Bucket Key<a name="viewing-bucket-key-settings"></a>

You can view settings for an S3 Bucket Key at the bucket or object level using the Amazon S3 console, REST API, AWS CLI, or AWS SDKs\.

S3 Bucket Keys decrease request traffic from Amazon S3 to AWS KMS and reduce the cost of server\-side encryption using AWS Key Management Service \(SSE\-KMS\)\. For more information, see [Reducing the cost of SSE\-KMS with Amazon S3 Bucket Keys](bucket-key.md)\. 

To view S3 Bucket Key settings for a bucket or an object that has inherited S3 Bucket Key settings from the bucket configuration, you need permission to perform the `s3:GetEncryptionConfiguration` action\. For more information, see [GetBucketEncryption](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketEncryption.html) in the *Amazon Simple Storage Service API Reference*\. 

## Using the S3 console<a name="bucket-key-settings"></a>

In the S3 console, you can view the S3 Bucket Key settings for your bucket or object\. S3 Bucket Key settings are inherited from the bucket configuration unless the source objects already has an S3 Bucket Key configured\.

Objects and folders in the same bucket can have different S3 Bucket Key settings\. For example, if you upload an object using the REST API and enable an S3 Bucket Key for the object, the object retains its S3 Bucket Key setting in the destination bucket, even if S3 Bucket Key is disabled in the destination bucket\. As another example, if you enable an S3 Bucket Key for an existing bucket, objects that are already in the bucket do not use an S3 Bucket Key\. However, new objects have an S3 Bucket Key enabled\. 

**To view bucket\-level an S3 Bucket Key setting**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the bucket that you want to enable an S3 Bucket Key for\.

1. Choose **Properties**\.

1. In the **Default encryption** section, under **Bucket Key**, you see the S3 Bucket Key setting for your bucket\.

   If you can’t see the S3 Bucket Key setting, you might not have permission to perform the `s3:GetEncryptionConfiguration` action\. For more information, see [GetBucketEncryption](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketEncryption.html) in the *Amazon Simple Storage Service API Reference*\. 

**To view the S3 Bucket Key setting for your object**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the bucket that you want to enable an S3 Bucket Key for\. 

1. In the **Objects** list, choose your object name\.

1. On the **Details** tab, under **Server\-side encryption settings**, choose **Edit**\. 

   Under **Bucket Key**, you see the S3 Bucket Key setting for your object but you cannot edit it\. 

## Using the REST API<a name="bucket-key-settings-rest"></a>

**To return bucket\-level S3 Bucket Key settings**  
To return encryption information for a bucket, including settings for an S3 Bucket Key, use the `GetBucketEncryption` operation\. S3 Bucket Key settings are returned in the response body in the `ServerSideEncryptionConfiguration` with the `BucketKeyEnabled` setting\. For more information, see [GetBucketEncryption](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketEncryption.html) in the *Amazon S3 API Reference*\. 

**To return object\-level settings for an S3 Bucket Key**  
To return the S3 Bucket Key status for an object, use the `HeadObject` operation\. `HeadObject` returns the `x-amz-server-side-encryption-bucket-key-enabled` response header to show whether an S3 Bucket Key is enabled or disabled for the object\. For more information, see [HeadObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadObject.html) in the *Amazon S3 API Reference*\. 

The following API operations also return the `x-amz-server-side-encryption-bucket-key-enabled` response header if an S3 Bucket Key is configured for an object: 
+ [PutObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html) 
+ [PostObject](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOST.html) 
+ [CopyObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html) 
+ [CreateMultipartUpload](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CreateMultipartUpload.html) 
+ [UploadPartCopy](https://docs.aws.amazon.com/AmazonS3/latest/API/API_UploadPartCopy.html) 
+ [UploadPart](https://docs.aws.amazon.com/AmazonS3/latest/API/API_UploadPart.html) 
+ [CompleteMultipartUpload](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CompleteMultipartUpload.html) 
+ [GetObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html) 