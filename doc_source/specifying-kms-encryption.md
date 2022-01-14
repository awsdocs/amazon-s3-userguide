# Specifying server\-side encryption with AWS KMS \(SSE\-KMS\)<a name="specifying-kms-encryption"></a>

When you create an object, you can specify the use of server\-side encryption with AWS Key Management Service \(AWS KMS\) keys to encrypt your data\. This encryption is known as SSE\-KMS\. You can apply encryption when you are either uploading a new object or copying an existing object\. 

You can specify SSE\-KMS using the Amazon S3 console, REST API operations, AWS SDKs, and AWS Command Line Interface \(AWS CLI\)\. For more information, see the following topics\. 

**Note**  
You can use multi\-Region AWS KMS keys in Amazon S3\. However, Amazon S3 currently treats multi\-Region keys as though they were single\-Region keys, and does not use the multi\-Region features of the key\. For more information, see [ Using multi\-Region keys](https://docs.aws.amazon.com/kms/latest/developerguide/multi-region-keys-overview.html) in *AWS Key Management Service Developer Guide*\.

## Using the S3 console<a name="add-object-encryption-kms"></a>

This topic describes how to set or change the type of encryption an object using the Amazon S3 console\.

**Note**  
If you change an object's encryption, a new object is created to replace the old one\. If S3 Versioning is enabled, a new version of the object is created, and the existing object becomes an older version\. The role that changes the property also becomes the owner of the new object or \(object version\)\. 

**To add or change encryption for an object**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that contains the object\.

1. In the **Objects** list, choose the name of the object that you want to add or change encryption for\.

   The **Object overview** opens, displaying the properties for your object\.

1. Under **Server\-side encryption settings**, choose **Edit**\.

   The **Edit server\-side encryption** page opens

1. To enable server\-side encryption for your object, under **Server\-side encryption**, choose **Enable**\.

1. Under **Encryption key type**, choose **AWS Key Management Service key \(SSE\-KMS\)**\.
**Important**  
If you use the AWS KMS option for your default encryption configuration, you are subject to the RPS \(requests per second\) limits of AWS KMS\. For more information about AWS KMS limits and how to request a limit increase, see [AWS KMS limits](https://docs.aws.amazon.com/kms/latest/developerguide/limits.html)\. 

1. Under **AWS KMS key**, choose one of the following:
   + **AWS managed key \(aws/s3\)**
   + **Choose from your AWS KMS keys**, and choose your **KMS key**\.
   + **Enter KMS master key ARN**, and enter your AWS KMS key Amazon Resource Name \(ARN\)\.
**Important**  
You can use only AWS KMS keys that are enabled in the same AWS Region as the bucket\. When you choose **Choose from your AWS KMS keys**, the S3 console lists only 100 KMS keys per Region\. If you have more than 100 KMS keys in the same Region, you can see only the first 100 KMS keys in the S3 console\. To use a KMS key that is not listed in the console, choose **Custom KMS ARN**, and enter the KMS key ARN\.  
When you use an AWS KMS key for server\-side encryption in Amazon S3, you must choose a KMS key that is enabled in the same Region as your bucket\. Additionally, Amazon S3 supports only symmetric encryption KMS keys, and not asymmetric KMS keys\. For more information, see [Using Symmetric and Asymmetric Keys](https://docs.aws.amazon.com/kms/latest/developerguide/symmetric-asymmetric.html) in the *AWS Key Management Service Developer Guide*\.

   For more information about creating an AWS KMS key, see [Creating Keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\. For more information about using AWS KMS with Amazon S3, see [Protecting data using server\-side encryption with AWS Key Management Service \(SSE\-KMS\)](UsingKMSEncryption.md)\.

1. Choose **Save changes**\.

**Note**  
This action applies encryption to all specified objects\. When encrypting folders, wait for the save operation to finish before adding new objects to the folder\.

## Using the REST API<a name="KMSUsingRESTAPI"></a>

When you create an object—that is, when you upload a new object or copy an existing object—you can specify the use of server\-side encryption with AWS KMS keys to encrypt your data\. To do this, add the `x-amz-server-side-encryption` header to the request\. Set the value of the header to the encryption algorithm `aws:kms`\. Amazon S3 confirms that your object is stored using SSE\-KMS by returning the response header `x-amz-server-side-encryption`\. 

If you specify the `x-amz-server-side-encryption` header with a value of `aws:kms`, you can also use the following request headers:
+ `x-amz-server-side-encryption-aws-kms-key-id`
+ `x-amz-server-side-encryption-context`
+ `x-amz-server-side-encryption-bucket-key-enabled`

**Topics**
+ [Amazon S3 REST API operations that support SSE\-KMS](#sse-request-headers-kms)
+ [Encryption context \(x\-amz\-server\-side\-encryption\-context\)](#s3-kms-encryption-context)
+ [AWS KMS key ID \(x\-amz\-server\-side\-encryption\-aws\-kms\-key\-id\)](#s3-kms-key-id-api)
+ [S3 Bucket Keys \(x\-amz\-server\-side\-encryption\-aws\-bucket\-key\-enabled\)](#bucket-key-api)

### Amazon S3 REST API operations that support SSE\-KMS<a name="sse-request-headers-kms"></a>

The following REST API operations accept the `x-amz-server-side-encryption`, `x-amz-server-side-encryption-aws-kms-key-id`, and `x-amz-server-side-encryption-context` request headers\.
+ [ PUT object](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html)— When you upload data using the PUT API operation, you can specify these request headers\. 
+ [ COPY object](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html)— When you copy an object, you have both a source object and a target object\. When you pass SSE\-KMS headers with the COPY operation, they are applied only to the target object\. When copying an existing object, regardless of whether the source object is encrypted or not, the destination object is not encrypted unless you explicitly request server\-side encryption\.
+ [POST Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOST.html)— When you use a POST operation to upload an object, instead of the request headers, you provide the same information in the form fields\.
+ [ Create Multipart Upload](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CreateMultipartUpload.html)— When you upload large objects using the multipart upload API, you can specify these headers\. You specify these headers in the initiate request\.

The response headers of the following REST API operations return the `x-amz-server-side-encryption` header when an object is stored using server\-side encryption\.
+ [PUT object](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html)
+ [ COPY object](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html)
+ [POST Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOST.html)
+ [ Create Multipart Upload](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CreateMultipartUpload.html)
+ [ Upload Part](https://docs.aws.amazon.com/AmazonS3/latest/API/API_UploadPart.html)
+ [ Upload Part Copy](https://docs.aws.amazon.com/AmazonS3/latest/API/API_UploadPartCopy.html)
+ [ Complete Multipart Upload](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CompleteMultipartUpload.html)
+ [ Get Object](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html)
+ [ Head Object](https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadObject.html)

**Important**  
All GET and PUT requests for an object protected by AWS KMS fail if you don't make them using Secure Sockets Layer \(SSL\), Transport Layer Security \(TLS\), or Signature Version 4\.
If your object uses SSE\-KMS, don't send encryption request headers for `GET` requests and `HEAD` requests, or you’ll get an HTTP 400 BadRequest error\.

### Encryption context \(x\-amz\-server\-side\-encryption\-context\)<a name="s3-kms-encryption-context"></a>

If you specify `x-amz-server-side-encryption:aws:kms`, the Amazon S3 API supports an encryption context with the `x-amz-server-side-encryption-context` header\. An encryption context is a set of key\-value pairs that contain additional contextual information about the data\.

Amazon S3 automatically uses the object or bucket Amazon Resource Name \(ARN\) as the encryption context pair\. If you use SSE\-KMS without enabling an S3 Bucket Key, you use the object ARN as your encryption context, for example, `arn:aws:s3:::object_ARN`\. However, if you use SSE\-KMS and enable an S3 Bucket Key, you use the bucket ARN for your encryption context, for example, `arn:aws:s3:::bucket_ARN`\. 

You can optionally provide an additional encryption context pair using the `x-amz-server-side-encryption-context` header\. However, because the encryption context is not encrypted, make sure it does not include sensitive information\. Amazon S3 stores this additional key pair alongside the default encryption context\.

For information about the encryption context in Amazon S3, see [Encryption context](UsingKMSEncryption.md#encryption-context)\. For general information about the encryption context, see [AWS Key Management Service Concepts \- Encryption context](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#encrypt_context) in the *AWS Key Management Service Developer Guide*\. 

### AWS KMS key ID \(x\-amz\-server\-side\-encryption\-aws\-kms\-key\-id\)<a name="s3-kms-key-id-api"></a>

You can use the `x-amz-server-side-encryption-aws-kms-key-id` header to specify the ID of the customer managed key used to protect the data\. If you specify `x-amz-server-side-encryption:aws:kms`, but don't provide `x-amz-server-side-encryption-aws-kms-key-id`, Amazon S3 uses the AWS managed key to protect the data\. If you want to use a customer managed key, you must provide the `x-amz-server-side-encryption-aws-kms-key-id` header of the customer managed key\.

**Important**  
When you use an AWS KMS key for server\-side encryption in Amazon S3, you must choose a symmetric encryption KMS key\. Amazon S3 supports only symmetric keys and not asymmetric keys\. For more information, see [Using Symmetric and Asymmetric Keys](https://docs.aws.amazon.com/kms/latest/developerguide/symmetric-asymmetric.html) in the *AWS Key Management Service Developer Guide*\.

### S3 Bucket Keys \(x\-amz\-server\-side\-encryption\-aws\-bucket\-key\-enabled\)<a name="bucket-key-api"></a>

You can use the `x-amz-server-side-encryption-aws-bucket-key-enabled` request header to enable or disable an S3 Bucket Key at the object\-level\. S3 Bucket Keys can reduce your AWS KMS request costs by decreasing the request traffic from Amazon S3 to AWS KMS\. For more information, see [Reducing the cost of SSE\-KMS with Amazon S3 Bucket Keys](bucket-key.md)\.

If you specify `x-amz-server-side-encryption:aws:kms`, but don't provide `x-amz-server-side-encryption-aws-bucket-key-enabled`, your object uses the S3 Bucket Key settings for the destination bucket to encrypt your object\. For more information, see [Configuring an S3 Bucket Key at the object level using Batch Operations, REST API, AWS SDKs, or AWS CLI](configuring-bucket-key-object.md)\.

## Using the AWS SDKs<a name="kms-using-sdks"></a>

When using AWS SDKs, you can request Amazon S3 to use AWS KMS keys\. This section provides examples of using the AWS SDKs for Java and \.NET\. For information about other SDKs, go to [Sample Code and Libraries](https://aws.amazon.com/code)\.

**Important**  
When you use an AWS KMS key for server\-side encryption in Amazon S3, you must choose a symmetric encryption KMS key\. Amazon S3 supports only symmetric keys and not asymmetric keys\. For more information, see [Using Symmetric and Asymmetric Keys](https://docs.aws.amazon.com/kms/latest/developerguide/symmetric-asymmetric.html) in the *AWS Key Management Service Developer Guide*\.

### Copy operation<a name="kms-copy-operation"></a>

When copying objects, you add the same request properties \(`ServerSideEncryptionMethod` and `ServerSideEncryptionKeyManagementServiceKeyId`\) to request Amazon S3 to use an AWS KMS key\. For more information about copying objects, see [Copying objects](copy-object.md)\. 

### Put operation<a name="kms-put-operation"></a>

------
#### [ Java ]

When uploading an object using the AWS SDK for Java, you can request Amazon S3 to use an AWS KMS key by adding the `SSEAwsKeyManagementParams` property as shown in the following request\.

```
PutObjectRequest putRequest = new PutObjectRequest(bucketName,
   keyName, file).withSSEAwsKeyManagementParams(new SSEAwsKeyManagementParams());
```

In this case, Amazon S3 uses the AWS managed key \(see [Protecting data using server\-side encryption with AWS Key Management Service \(SSE\-KMS\)](UsingKMSEncryption.md)\. You can optionally create a symmetric KMS key and specify that in the request\.

```
PutObjectRequest putRequest = new PutObjectRequest(bucketName,
   keyName, file).withSSEAwsKeyManagementParams(new SSEAwsKeyManagementParams(keyID));
```

For more information about creating customer managed keys, see [Programming the AWS KMS API](https://docs.aws.amazon.com/kms/latest/developerguide/programming-top.html) in the *AWS Key Management Service Developer Guide*\.

For working code examples of uploading an object, see the following topics\. You will need to update those code examples and provide encryption information as shown in the preceding code fragment\.
+ For uploading an object in a single operation, see [Uploading objects](upload-objects.md)\.
+ For a multipart upload, see the following topics:
  + Using the high\-level multipart upload API, see [Uploading an object using multipart upload](mpu-upload-object.md)\. 
  + Using the low\-level multipart upload API, see [Using the AWS SDKs \(low\-level\-level API\)](mpu-upload-object.md#mpu-upload-low-level)\.

------
#### [ \.NET ]

When uploading an object using the AWS SDK for \.NET, you can request Amazon S3 to use an AWS KMS key by adding the `ServerSideEncryptionMethod` property as shown in the following request\.

```
PutObjectRequest putRequest = new PutObjectRequest
 {
     BucketName = bucketName,
     Key = keyName,
     // other properties.
     ServerSideEncryptionMethod = ServerSideEncryptionMethod.AWSKMS
 };
```

In this case, Amazon S3 uses the AWS managed key\. For more information, see [Protecting data using server\-side encryption with AWS Key Management Service \(SSE\-KMS\)](UsingKMSEncryption.md)\. You can optionally create your own symmetric customer managed key and specify that in the request\. 

```
PutObjectRequest putRequest1 = new PutObjectRequest
{
  BucketName = bucketName,
  Key = keyName,
  // other properties.
  ServerSideEncryptionMethod = ServerSideEncryptionMethod.AWSKMS,
  ServerSideEncryptionKeyManagementServiceKeyId = keyId
};
```

For more information about creating customer managed keys, see [Programming the AWS KMS API](https://docs.aws.amazon.com/kms/latest/developerguide/programming-top.html) in the *AWS Key Management Service Developer Guide*\. 

For working code examples of uploading an object, see the following topics\. You will need to update these code examples and provide encryption information as shown in the preceding code fragment\.
+ For uploading an object in a single operation, see [Uploading objects](upload-objects.md)\.
+ For multipart upload, see the following topics:
  + Using the high\-level multipart upload API, see [Uploading an object using multipart upload](mpu-upload-object.md)\. 
  + Using the low\-level multipart upload API, see [Uploading an object using multipart upload](mpu-upload-object.md)\.

------

### Presigned URLs<a name="kms-presigned-urls"></a>

------
#### [ Java ]

When creating a presigned URL for an object encrypted with an AWS KMS key, you must explicitly specify Signature Version 4\.

```
ClientConfiguration clientConfiguration = new ClientConfiguration();
clientConfiguration.setSignerOverride("AWSS3V4SignerType");
AmazonS3Client s3client = new AmazonS3Client(
        new ProfileCredentialsProvider(), clientConfiguration);
...
```

For a code example, see [Sharing an object with a presigned URL](ShareObjectPreSignedURL.md)\. 

------
#### [ \.NET ]

When creating a presigned URL for an object encrypted with an AWS KMS key, you must explicitly specify Signature Version 4\.

```
AWSConfigs.S3Config.UseSignatureVersion4 = true;
```

For a code example, see [Sharing an object with a presigned URL](ShareObjectPreSignedURL.md)\.

------