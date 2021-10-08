# Protecting Data Using Server\-Side Encryption with KMS keys Stored in AWS Key Management Service \(SSE\-KMS\)<a name="UsingKMSEncryption"></a>

Server\-side encryption is the encryption of data at its destination by the application or service that receives it\. AWS Key Management Service \(AWS KMS\) is a service that combines secure, highly available hardware and software to provide a key management system scaled for the cloud\. Amazon S3 uses AWS KMS keys to encrypt your Amazon S3 objects\. AWS KMS encrypts only the object data\. Any object metadata is not encrypted\. 

If you use KMS keys, you use AWS KMS via the [AWS Management Console](https://console.aws.amazon.com/kms) or [AWS KMS APIs](https://docs.aws.amazon.com/kms/latest/APIReference/) to centrally create KMS keys, define the policies that control how KMS keys can be used, and audit their usage to prove that they are being used correctly\. You can use these KMS keys to protect your data in Amazon S3 buckets\. When you use SSE\-KMS encryption with an S3 bucket, the AWS KMS keys must be in the same Region as the bucket\.

There are additional charges for using AWS KMS keys\. For more information, see [AWS KMS key concepts](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#kms_keys) in the *AWS Key Management Service Developer Guide* and [AWS KMS pricing](https://aws.amazon.com/kms/pricing)\.

**Important**  
To upload an object encrypted with an AWS KMS key to Amazon S3, you need `kms:Decrypt` and `kms:GenerateDataKey` permissions on the key\. To download, an object encrypted with an AWS KMS key, you need `kms:Decrypt` permissions\. For information about AWS KMS permissions and multipart upload, see [Multipart upload API and permissions](mpuoverview.md#mpuAndPermissions)\.

## AWS KMS keys and customer managed keys<a name="aws-managed-customer-managed-keys"></a>

When you use server\-side encryption with AWS KMS \(SSE\-KMS\), you can use the default [AWS managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-managed-cmk), or you can specify a [customer managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) that you have already created\. 

If you don't specify a customer managed key, Amazon S3 automatically creates an AWS KMS key in your AWS account the first time that you add an object encrypted with SSE\-KMS to a bucket\. By default, Amazon S3 uses this KMS key for SSE\-KMS\. 

If you want to use a customer managed key for SSE\-KMS, create the customer managed key before you configure SSE\-KMS\. Then, when you configure SSE\-KMS for your bucket, specify the existing customer managed key\. 

Creating a customer managed key gives you more flexibility and control\. For example, you can create, rotate, and disable customer managed keys\. You can also define access controls and audit the customer managed key that you use to protect your data\. For more information about customer managed and AWS managed keys, see [AWS KMS concepts](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html) in the *AWS Key Management Service Developer Guide*\.

**Important**  
When you use an AWS KMS key for server\-side encryption in Amazon S3, you must choose a symmetric key\. Amazon S3 only supports symmetric keys and not asymmetric keys\. For more information, see [Using Symmetric and Asymmetric Keys](https://docs.aws.amazon.com/kms/latest/developerguide/symmetric-asymmetric.html) in the *AWS Key Management Service Developer Guide*\.

## Amazon S3 Bucket Keys<a name="sse-kms-bucket-keys"></a>

When you configure server\-side encryption using AWS KMS \(SSE\-KMS\), you can configure your bucket to use S3 Bucket Keys for SSE\-KMS\. This bucket\-level key for SSE\-KMS can reduce your KMS request costs by up to 99 percent by decreasing the request traffic from Amazon S3 to AWS KMS\. 

When you configure your bucket to use S3 Bucket Keys for SSE\-KMS on new objects, AWS KMS generates a bucket\-level key that is used to create unique [data keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#data-keys) for objects in the bucket\. This bucket key is used for a time\-limited period within Amazon S3, further reducing the need for Amazon S3 to make requests to AWS KMS to complete encryption operations\. For more information about using S3 Bucket Keys, see [Reducing the cost of SSE\-KMS with Amazon S3 Bucket Keys](bucket-key.md)\.

## AWS Signature Version 4<a name="aws-signature-version-4-sse-kms"></a>

If you are uploading or accessing objects encrypted by SSE\-KMS, you need to use AWS Signature Version 4 for added security\. For more information on how to do this using an AWS SDK, see [Specifying the Signature Version in Request Authentication](UsingAWSSDK.md#specify-signature-version)\.

**Important**  
All GET and PUT requests for an object protected by AWS KMS fail if they are not made via SSL or TLS, or if they are not made using SigV4\.

## SSE\-KMS highlights<a name="sse-kms-highlights"></a>

The highlights of SSE\-KMS are as follows:
+ You can choose a customer managed key that you create and manage, or you can choose an AWS managed key that Amazon S3 creates in your AWS account and manages for you\. Like a customer managed key, your AWS managed key is unique to your AWS account and Region\. Only Amazon S3 has permission to use this KMS key on your behalf\. Amazon S3 only supports symmetric KMS keys\.
+ You can create, rotate, and disable auditable customer managed keys from the AWS KMS console\. 
+ The `ETag` in the response is not the MD5 of the object data\.
+ The data keys used to encrypt your data are also encrypted and stored alongside the data that they protect\. 
+ The security controls in AWS KMS can help you meet encryption\-related compliance requirements\.

## Requiring server\-side encryption<a name="require-sse-kms"></a>

To require server\-side encryption of all objects in a particular Amazon S3 bucket, you can use a policy\. For example, the following bucket policy denies upload object \(`s3:PutObject`\) permission to everyone if the request does not include the `x-amz-server-side-encryption` header requesting server\-side encryption with SSE\-KMS\.

```
 1. {
 2.    "Version":"2012-10-17",
 3.    "Id":"PutObjectPolicy",
 4.    "Statement":[{
 5.          "Sid":"DenyUnEncryptedObjectUploads",
 6.          "Effect":"Deny",
 7.          "Principal":"*",
 8.          "Action":"s3:PutObject",
 9.          "Resource":"arn:aws:s3:::awsexamplebucket1/*",
10.          "Condition":{
11.             "StringNotEquals":{
12.                "s3:x-amz-server-side-encryption":"aws:kms"
13.             }
14.          }
15.       }
16.    ]
17. }
```

To require that a particular AWS KMS key be used to encrypt the objects in a bucket, you can use the `s3:x-amz-server-side-encryption-aws-kms-key-id` condition key\. To specify the KMS key, you must use a key Amazon Resource Name \(ARN\) that is in the "`arn:aws:kms:region:acct-id:key/key-id"` format\.

**Note**  
When you upload an object, you can specify the KMS key using the `x-amz-server-side-encryption-aws-kms-key-id` header\. If the header is not present in the request, Amazon S3 assumes the AWS managed key\. Regardless, the AWS KMS key ID that Amazon S3 uses for object encryption must match the AWS KMS key ID in the policy, otherwise Amazon S3 denies the request\.

For a complete list of Amazon S3‐specific condition keys and more information about specifying condition keys, see [Amazon S3 condition key examples](amazon-s3-policy-keys.md)\.

## Encryption context<a name="encryption-context"></a>

Amazon S3 supports an encryption context with the `x-amz-server-side-encryption-context` header\. An encryption context is an optional set of key\-value pairs that can contain additional contextual information about the data\. 

For information about the encryption context in Amazon S3, see [Encryption context](#encryption-context)\. For general information about encryption context, see [AWS Key Management Service Concepts \- Encryption Context](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#encrypt_context) in the *AWS Key Management Service Developer Guide*\. 

The encryption context can be any value that you want, provided that the header adheres to the Base64\-encoded JSON format\. However, because the encryption context is not encrypted and because it is logged if AWS CloudTrail logging is turned on, the encryption context should not include sensitive information\. We further recommend that your context describe the data being encrypted or decrypted so that you can better understand the CloudTrail events produced by AWS KMS\.

In Amazon S3, the object or bucket Amazon Resource Name \(ARN\) is commonly used as an encryption context\. If you use SSE\-KMS without enabling an S3 Bucket Key, you use the object ARN as your encryption context, for example, `arn:aws:s3:::object_ARN`\. However, if you use SSE\-KMS and enable an S3 Bucket Key, you use the bucket ARN for your encryption context, for example, `arn:aws:s3:::bucket_ARN`\. For more information about S3 Bucket Keys, see [Reducing the cost of SSE\-KMS with Amazon S3 Bucket Keys](bucket-key.md)\.

If the key `aws:s3:arn` is not already in the encryption context, Amazon S3 can append a predefined key of `aws:s3:arn` to the encryption context that you provide\. Amazon S3 appends this predefined key when it processes your requests\. If you use SSE\-KMS without an S3 Bucket Key, the value is equal to the object ARN\. If you use SSE\-KMS with an S3 Bucket Key enabled, the value is equal to the bucket ARN\. 

You can use this predefined key to track relevant requests in CloudTrail\. So you can always see which Amazon S3 ARN was used with which encryption key\. You can use CloudTrail logs to ensure that the encryption context is not identical between different Amazon S3 objects and buckets, which provides additional security\. Your full encryption context will be validated to have the value equal to the object or bucket ARN\. 

**Topics**
+ [AWS KMS keys and customer managed keys](#aws-managed-customer-managed-keys)
+ [Amazon S3 Bucket Keys](#sse-kms-bucket-keys)
+ [AWS Signature Version 4](#aws-signature-version-4-sse-kms)
+ [SSE\-KMS highlights](#sse-kms-highlights)
+ [Requiring server\-side encryption](#require-sse-kms)
+ [Encryption context](#encryption-context)
+ [Specifying server\-side encryption with AWS KMS \(SSE\-KMS\)](specifying-kms-encryption.md)
+ [Reducing the cost of SSE\-KMS with Amazon S3 Bucket Keys](bucket-key.md)