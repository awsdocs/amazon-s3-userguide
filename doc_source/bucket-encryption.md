# Setting default server\-side encryption behavior for Amazon S3 buckets<a name="bucket-encryption"></a>

**Important**  
Amazon S3 now applies server\-side encryption with Amazon S3 managed keys \(SSE\-S3\) as the base level of encryption for every bucket in Amazon S3\. Starting January 5, 2023, all new object uploads to Amazon S3 are automatically encrypted at no additional cost and with no impact on performance\. The automatic encryption status for S3 bucket default encryption configuration and for new object uploads is available in AWS CloudTrail logs, S3 Inventory, S3 Storage Lens, the Amazon S3 console, and as an additional Amazon S3 API response header in the AWS Command Line Interface and AWS SDKs\. For more information, see [Default encryption FAQ](https://docs.aws.amazon.com/AmazonS3/latest/userguide/default-encryption-faq.html)\.

All Amazon S3 buckets have encryption configured by default, and objects are automatically encrypted by using server\-side encryption with Amazon S3 managed keys \(SSE\-S3\)\. This encryption setting applies to all objects in your Amazon S3 buckets\.

If you need more control over your keys, such as managing key rotation and access policy grants, you can choose to use server\-side encryption with AWS Key Management Service \(AWS KMS\) keys \(SSE\-KMS\)\. For more information about editing KMS keys, see in [Editing keys](https://docs.aws.amazon.com/kms/latest/developerguide/editing-keys.html) in *AWS Key Management Service Developer Guide*\. 

**Note**  
We've changed buckets to encrypt new object uploads automatically\. If you previously created a bucket without default encryption, Amazon S3 will enable encryption by default for the bucket using SSE\-S3\. There will be no changes to the default encryption configuration for an existing bucket that already has SSE\-S3 or SSE\-KMS configured\. If you want to encrypt your objects with SSE\-KMS, you must change the encryption type in your bucket settings\. For more information, see [Protecting data using server\-side encryption with AWS Key Management Service keys \(SSE\-KMS\)](UsingKMSEncryption.md)\. 

When you configure your bucket to use default encryption with SSE\-KMS, you can also enable S3 Bucket Keys to decrease request traffic from Amazon S3 to AWS KMS and reduce the cost of encryption\. For more information, see [Reducing the cost of SSE\-KMS with Amazon S3 Bucket Keys](bucket-key.md)\.

To identify buckets that have SSE\-KMS enabled for default encryption, you can use Amazon S3 Storage Lens metrics\. S3 Storage Lens is a cloud\-storage analytics feature that you can use to gain organization\-wide visibility into object\-storage usage and activity\. For more information, see [ Using S3 Storage Lens to protect your data](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-lens-data-protection.html?icmpid=docs_s3_user_guide_bucket-encryption.html)\.

When you use server\-side encryption, Amazon S3 encrypts an object before saving it to disk and decrypts it when you download the object\. For more information about protecting data using server\-side encryption and encryption\-key management, see [Protecting data using server\-side encryption](serv-side-encryption.md)\.

For more information about the permissions required for default encryption, see [PutBucketEncryption](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketEncryption.html) in the *Amazon Simple Storage Service API Reference*\.

You can configure the Amazon S3 default encryption behavior for an S3 bucket by using the Amazon S3 console, the AWS SDKs, the Amazon S3 REST API, and the AWS Command Line Interface \(AWS CLI\)\.

**Encrypting existing objects**  
To encrypt your existing unencrypted Amazon S3 objects, you can use Amazon S3 Batch Operations\. You provide S3 Batch Operations with a list of objects to operate on, and Batch Operations calls the respective API to perform the specified operation\. You can use the [Batch Operations Copy operation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/batch-ops-copy-object.html) to copy existing unencrypted objects and write them back to the same bucket as encrypted objects\. A single Batch Operations job can perform the specified operation on billions of objects\. For more information, see [Performing large\-scale batch operations on Amazon S3 objects](batch-ops.md) and the *AWS Storage Blog* post [Encrypting objects with Amazon S3 Batch Operations](http://aws.amazon.com/blogs/storage/encrypting-objects-with-amazon-s3-batch-operations/)\.

You can also encrypt existing objects by using the `CopyObject` API operation or the `copy-object` AWS Command Line Interface \(AWS CLI\) command\. For more information, see the *AWS Storage Blog* post [Encrypting existing Amazon S3 objects with the AWS CLI](http://aws.amazon.com/blogs/storage/encrypting-existing-amazon-s3-objects-with-the-aws-cli/)\.

**Note**  
Amazon S3 buckets with default bucket encryption using SSE\-KMS cannot be used as destination buckets for [Logging requests using server access logging](ServerLogs.md)\. Only SSE\-S3 default encryption is supported for server access log destination buckets\.

## Using SSE\-KMS encryption for cross\-account operations<a name="bucket-encryption-update-bucket-policy"></a>

When using encryption for cross\-account operations, be aware of the following:
+ If an AWS KMS key Amazon Resource Name \(ARN\) or alias is not provided at request time or through the bucket's default encryption configuration, the AWS managed key \(`aws/s3`\) is used\.
+ If you're uploading or accessing S3 objects by using AWS Identity and Access Management \(IAM\) principals that are in the same AWS account as your KMS key, you can use the AWS managed key \(`aws/s3`\)\. 
+ If you want to grant cross\-account access to your S3 objects, use a customer managed key\. You can configure the policy of a customer managed key to allow access from another account\.
+ If you're specifying your own KMS key, we recommend using a fully qualified KMS key ARN\. If you use a KMS key alias instead, AWS KMS resolves the key within the requester’s account\. This behavior can result in data encrypted with a KMS key that belongs to the requester, and not the bucket administrator\.
+ You must specify a key that you \(the requester\) have been granted `Encrypt` permission to\. For more information, see [Allow key users to use a KMS key for cryptographic operations](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html#key-policy-users-crypto) in the *AWS Key Management Service Developer Guide*\.

For more information about when to use customer managed keys and AWS managed KMS keys, see [Should I use an AWS managed key or a customer managed key to encrypt my objects in Amazon S3?](http://aws.amazon.com/premiumsupport/knowledge-center/s3-object-encryption-keys/)

## Using default encryption with replication<a name="bucket-encryption-replication"></a>

When you enable default encryption for a replication destination bucket, the following encryption behavior applies:
+ If objects in the source bucket are not encrypted, the replica objects in the destination bucket are encrypted using the default encryption settings of the destination bucket\. This results in the `ETag` of the source object being different from the `ETag` of the replica object\. You must update applications that use the `ETag` to accommodate for this difference\.
+ If objects in the source bucket are encrypted using SSE\-S3 or SSE\-KMS, the replica objects in the destination bucket use the same encryption as the source object encryption\. The default encryption settings of the destination bucket are not used\.

For more information about using default encryption with SSE\-KMS, see [Replicating encrypted objects \(SSE\-C, SSE\-S3, SSE\-KMS\)](replication-config-for-kms-objects.md)\.

## Using Amazon S3 Bucket Keys with default encryption<a name="bucket-key-default-encryption"></a>

When you configure your bucket to use SSE\-KMS as the default encryption behavior for new objects, you can also configure S3 Bucket Keys\. S3 Bucket Keys decrease the number of transactions from Amazon S3 to AWS KMS to reduce the cost of SSE\-KMS\. 

When you configure your bucket to use S3 Bucket Keys for SSE\-KMS on new objects, AWS KMS generates a bucket\-level key that is used to create a unique [data key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#data-keys) for objects in the bucket\. This S3 Bucket Key is used for a time\-limited period within Amazon S3, reducing the need for Amazon S3 to make requests to AWS KMS to complete encryption operations\. 

For more information about using S3 Bucket Keys, see [Using Amazon S3 Bucket Keys](bucket-key.md)\.