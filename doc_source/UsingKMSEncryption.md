# Protecting data using server\-side encryption with AWS Key Management Service \(SSE\-KMS\)<a name="UsingKMSEncryption"></a>

Server\-side encryption is the encryption of data at its destination by the application or service that receives it\. AWS Key Management Service \(AWS KMS\) is a service that combines secure, highly available hardware and software to provide a key management system scaled for the cloud\. Amazon S3 uses AWS KMS keys to encrypt your Amazon S3 objects\. AWS KMS encrypts only the object data\. Object metadata isn't encrypted, unless server\-side encryption with KMS keys is requested for the object\. If server\-side encryption is requested for the object, then the checksum is stored in encrypted form\.

If you use KMS keys, you can use AWS KMS through the [AWS Management Console](https://console.aws.amazon.com/kms) or the [AWS KMS APIs](https://docs.aws.amazon.com/kms/latest/APIReference/) to do the following: 
+ Centrally create KMS keys
+ Define the policies that control how KMS keys can be used
+ Audit their usage to prove that they are being used correctly



The security controls in AWS KMS can help you meet encryption\-related compliance requirements\. You can use these KMS keys to protect your data in Amazon S3 buckets\. When you use SSE\-KMS encryption with an S3 bucket, the AWS KMS keys must be in the same Region as the bucket\.

There are additional charges for using AWS KMS keys\. For more information, see [AWS KMS key concepts](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#kms_keys) in the *AWS Key Management Service Developer Guide* and [AWS KMS pricing](https://aws.amazon.com/kms/pricing)\.

**Permissions**  
To upload an object encrypted with an AWS KMS key to Amazon S3, you need `kms:GenerateDataKey` permissions on the key\. To download an object encrypted with an AWS KMS key, you need `kms:Decrypt` permissions\. For information about AWS KMS permissions required for multipart upload, see [Multipart upload API and permissions](mpuoverview.md#mpuAndPermissions)\.

**Topics**
+ [AWS KMS keys](#aws-managed-customer-managed-keys)
+ [Amazon S3 Bucket Keys](#sse-kms-bucket-keys)
+ [Requiring server\-side encryption](#require-sse-kms)
+ [Encryption context](#encryption-context)
+ [AWS Signature Version 4](#aws-signature-version-4-sse-kms)
+ [Specifying server\-side encryption with AWS KMS \(SSE\-KMS\)](specifying-kms-encryption.md)
+ [Reducing the cost of SSE\-KMS with Amazon S3 Bucket Keys](bucket-key.md)

## AWS KMS keys<a name="aws-managed-customer-managed-keys"></a>

When you use server\-side encryption with AWS KMS \(SSE\-KMS\), you can use the default [AWS managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-managed-cmk), or you can specify a [customer managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) that you have already created\. AWS KMS uses *envelope encryption* to further protect your data\. Envelope encryption is the practice of encrypting your plaintext data with a data key, and then encrypting that data key with a root key\. 

If you don't specify a customer managed key, Amazon S3 automatically creates an AWS KMS key in your AWS account the first time that you add an object encrypted with SSE\-KMS to a bucket\. By default, Amazon S3 uses this KMS key for SSE\-KMS\. 

If you want to use a customer managed key for SSE\-KMS, create the customer managed key before you configure SSE\-KMS\. Then, when you configure SSE\-KMS for your bucket, specify the existing customer managed key\. 

Creating a customer managed key gives you more flexibility and control\. For example, you can create, rotate, and disable customer managed keys\. You can also define access controls and audit the customer managed key that you use to protect your data\. For more information about customer managed and AWS managed keys, see [AWS KMS concepts](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html) in the *AWS Key Management Service Developer Guide*\.

If you choose to encrypt your data using a KMS key or customer managed key, AWS KMS and Amazon S3 perform the following actions:
+ Amazon S3 requests a plaintext [ data key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#data-keys) and a copy of the key encrypted under the specified KMS key\.
+ AWS KMS generates a data key, encrypts it under the KMS key, and sends both the plaintext data key and the encrypted data key to Amazon S3\.
+ Amazon S3 encrypts the data using the data key and removes the plaintext key from memory as soon as possible after use\.
+ Amazon S3 stores the encrypted data key as metadata with the encrypted data\.

When you request that your data be decrypted, Amazon S3 and AWS KMS perform the following actions:
+ Amazon S3 sends the encrypted data key to AWS KMS\.
+ AWS KMS decrypts the key by using the same KMS key and returns the plaintext data key to Amazon S3\.
+ Amazon S3 decrypts the ciphertext and removes the plaintext data key from memory as soon as possible\.

**Important**  
When you use an AWS KMS key for server\-side encryption in Amazon S3, you must choose a symmetric encryption KMS key\. Amazon S3 supports only symmetric encryption KMS keys and not asymmetric keys\. For more information, see [Using Symmetric and Asymmetric Keys](https://docs.aws.amazon.com/kms/latest/developerguide/symmetric-asymmetric.html) in the *AWS Key Management Service Developer Guide*\.

## Amazon S3 Bucket Keys<a name="sse-kms-bucket-keys"></a>

When you configure server\-side encryption using AWS KMS \(SSE\-KMS\), you can configure your bucket to use S3 Bucket Keys for SSE\-KMS\. Using a bucket\-level key for SSE\-KMS can reduce your AWS KMS request costs by up to 99 percent by decreasing the request traffic from Amazon S3 to AWS KMS\. 

When you configure your bucket to use S3 Bucket Keys for SSE\-KMS on new objects, AWS KMS generates a bucket\-level key that is used to create unique [data keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#data-keys) for objects in the bucket\. This bucket key is used for a time\-limited period within Amazon S3, further reducing the need for Amazon S3 to make requests to AWS KMS to complete encryption operations\. For more information about using S3 Bucket Keys, see [Reducing the cost of SSE\-KMS with Amazon S3 Bucket Keys](bucket-key.md)\.

## Requiring server\-side encryption<a name="require-sse-kms"></a>

To require server\-side encryption of all objects in a particular Amazon S3 bucket, you can use a policy\. For example, the following bucket policy denies the upload object \(`s3:PutObject`\) permission to everyone if the request does not include the `x-amz-server-side-encryption` header requesting server\-side encryption with SSE\-KMS\.

```
 1. {
 2.    "Version":"2012-10-17",
 3.    "Id":"PutObjectPolicy",
 4.    "Statement":[{
 5.          "Sid":"DenyUnEncryptedObjectUploads",
 6.          "Effect":"Deny",
 7.          "Principal":"*",
 8.          "Action":"s3:PutObject",
 9.          "Resource":"arn:aws:s3:::DOC-EXAMPLE-BUCKET1/*",
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
When you upload an object, you can specify the KMS key using the `x-amz-server-side-encryption-aws-kms-key-id` header\. If the header is not present in the request, Amazon S3 assumes that you want to use the AWS managed key\. Regardless, the AWS KMS key ID that Amazon S3 uses for object encryption must match the AWS KMS key ID in the policy, otherwise Amazon S3 denies the request\.

For a complete list of Amazon S3‐specific condition keys, see [Condition keys for Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/list_amazons3.html#amazons3-policy-keys)\.

## Encryption context<a name="encryption-context"></a>

An encryption context is a set of key\-value pairs that contains additional contextual information about the data\. The encryption context is not encrypted\. When an encryption context is specified for an encryption operation, Amazon S3 must specify the same encryption context for the decryption operation\. Otherwise, the decryption fails\. AWS KMS uses the encryption context as [additional authenticated data](https://docs.aws.amazon.com/crypto/latest/userguide/cryptography-concepts.html#term-aad) \(AAD\) to support [authenticated encryption](https://docs.aws.amazon.com/crypto/latest/userguide/cryptography-concepts.html#define-authenticated-encryption)\. For more information about the encryption context, see [Encryption context](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#encrypt_context) in the *AWS Key Management Service Developer Guide*\. 

Amazon S3 automatically uses the object or bucket Amazon Resource Name \(ARN\) as the encryption context pair: 
+ **If you use SSE\-KMS without enabling an S3 Bucket Key**, the object ARN is used as the encryption context\.

  ```
  arn:aws:s3:::object_ARN
  ```
+ **If you use SSE\-KMS and enable an S3 Bucket Key**, the bucket ARN is used as the encryption context\. For more information about S3 Bucket Keys, see [Reducing the cost of SSE\-KMS with Amazon S3 Bucket Keys](bucket-key.md)\.

  ```
  arn:aws:s3:::bucket_ARN
  ```

You can optionally provide an additional encryption context pair using the `x-amz-server-side-encryption-context` header in an [ s3:PutObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html#API_PutObject_RequestSyntax) request\. However, because the encryption context is not encrypted, make sure it does not include sensitive information\. Amazon S3 stores this additional key pair alongside the default encryption context\. When it processes your PUT request, Amazon S3 appends the default encryption context of `aws:s3:arn` to the one that you provide\. 

You can use the encryption context to identify and categorize your cryptographic operations\. You can also use the default encryption context ARN value to track relevant requests in AWS CloudTrail by viewing which Amazon S3 ARN was used with which encryption key\.

In the `requestParameters` field of a CloudTrail log file, the encryption context looks similar to the following one\. 

```
"encryptionContext": {
    "aws:s3:arn": "arn:aws:s3:::DOC-EXAMPLE-BUCKET1/file_name"
}
```

When you use SSE\-KMS with the optional S3 Bucket Keys feature, the encryption context value is the ARN of the bucket\.

```
"encryptionContext": {
    "aws:s3:arn": "arn:aws:s3:::DOC-EXAMPLE-BUCKET1"
}
```

## AWS Signature Version 4<a name="aws-signature-version-4-sse-kms"></a>

Signature Version 4 is the process of adding authentication information to AWS requests sent by HTTP\. For security, most requests to AWS must be signed with an access key, which consists of an access key ID and secret access key\. These two keys are commonly referred to as your security credentials\. For more information, see [Authenticating Requests \(AWS Signature Version 4\)](https://docs.aws.amazon.com/AmazonS3/latest/API/sig-v4-authenticating-requests.html) and [Signature Version 4 signing process](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html)\.

**Important**  
All GET and PUT requests for AWS KMS encrypted objects must be made using Secure Sockets Layer \(SSL\) or Transport Layer Security \(TLS\)\. Requests must also be signed using valid credentials, such as AWS Signature Version 4 \(or AWS Signature Version 2\)\.
If your object uses SSE\-KMS, don't send encryption request headers for `GET` requests and `HEAD` requests, or you’ll get an HTTP 400 BadRequest error\.

**Topics**
+ [AWS KMS keys](#aws-managed-customer-managed-keys)
+ [Amazon S3 Bucket Keys](#sse-kms-bucket-keys)
+ [Requiring server\-side encryption](#require-sse-kms)
+ [Encryption context](#encryption-context)
+ [AWS Signature Version 4](#aws-signature-version-4-sse-kms)
+ [Specifying server\-side encryption with AWS KMS \(SSE\-KMS\)](specifying-kms-encryption.md)
+ [Reducing the cost of SSE\-KMS with Amazon S3 Bucket Keys](bucket-key.md)