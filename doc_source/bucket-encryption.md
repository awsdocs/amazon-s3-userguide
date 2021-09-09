# Setting default server\-side encryption behavior for Amazon S3 buckets<a name="bucket-encryption"></a>

With Amazon S3 default encryption, you can set the default encryption behavior for an S3 bucket so that all new objects are encrypted when they are stored in the bucket\. The objects are encrypted using server\-side encryption with either Amazon S3\-managed keys \(SSE\-S3\) or AWS KMS keys stored in AWS Key Management Service \(AWS KMS\) \(SSE\-KMS\)\. 

When you configure your bucket to use default encryption with SSE\-KMS, you can also enable S3 Bucket Keys to decrease request traffic from Amazon S3 to AWS Key Management Service \(AWS KMS\) and reduce the cost of encryption\. For more information, see [Reducing the cost of SSE\-KMS with Amazon S3 Bucket Keys](bucket-key.md)\.

When you use server\-side encryption, Amazon S3 encrypts an object before saving it to disk and decrypts it when you download the objects\. For more information about protecting data using server\-side encryption and encryption key management, see [Protecting data using server\-side encryption](serv-side-encryption.md)\.

For more information about permissions required for default encryption, see [PutBucketEncryption](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketEncryption.html) in the *Amazon Simple Storage Service API Reference*\.

To set up default encryption on a bucket, you can use the Amazon S3 console, AWS CLI, AWS SDKs, or the REST API\. For more information, see [Enabling Amazon S3 default bucket encryption](default-bucket-encryption.md)\.

**Encrypting existing objects**  
To encrypt your existing Amazon S3 objects, you can use Amazon S3 Batch Operations\. You provide S3 Batch Operations with a list of objects to operate on, and Batch Operations calls the respective API to perform the specified operation\. You can use the [Batch Operations Copy operation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/batch-ops-copy-object.html) to copy existing unencrypted objects and write them back to the same bucket as encrypted objects\. A single Batch Operations job can perform the specified operation on billions of objects\. For more information, see [Performing large\-scale batch operations on Amazon S3 objects](batch-ops.md) and the *AWS Storage Blog* post [Encrypting objects with Amazon S3 Batch Operations](http://aws.amazon.com/blogs/storage/encrypting-objects-with-amazon-s3-batch-operations/)\.

You can also encrypt existing objects using the Copy Object API\. For more information, see the *AWS Storage Blog* post [Encrypting existing Amazon S3 objects with the AWS CLI](http://aws.amazon.com/blogs/storage/encrypting-existing-amazon-s3-objects-with-the-aws-cli/)\.

**Note**  
Amazon S3 buckets with default bucket encryption using SSE\-KMS cannot be used as destination buckets for [Logging requests using server access logging](ServerLogs.md)\. Only SSE\-S3 default encryption is supported for server access log destination buckets\.

## Using encryption for cross\-account operations<a name="bucket-encryption-update-bucket-policy"></a>

Be aware of the following when using encryption for cross\-account operations:
+ The AWS managed key \(aws/s3\) is used when a AWS KMS key Amazon Resource Name \(ARN\) or alias is not provided at request time, nor via the bucket's default encryption configuration\.
+ If you're uploading or accessing S3 objects using AWS Identity and Access Management \(IAM\) principals that are in the same AWS account as your KMS key, you can use the AWS managed key \(aws/s3\)\. 
+ Use a customer managed key if you want to grant cross\-account access to your S3 objects\. You can configure the policy of a customer managed key to allow access from another account\.
+ If specifying your own KMS key, you should use a fully qualified KMS key key ARN\. When using a KMS key alias, be aware that AWS KMS will resolve the key within the requester’s account\. This can result in data encrypted with a KMS key that belongs to the requester, and not the bucket administrator\.
+ You must specify a key that you \(the requester\) have been granted `Encrypt` permission to\. For more information, see [Allows key users to use a KMS key for cryptographic operations](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html#key-policy-users-crypto) in the *AWS Key Management Service Developer Guide*\.

For more information about when to use customer managed keys and the AWS managed KMS keys, see [Should I use an AWS managed key or a customer managed key key to encrypt my objects on Amazon S3?](http://aws.amazon.com/premiumsupport/knowledge-center/s3-object-encrpytion-keys/)

## Using default encryption with replication<a name="bucket-encryption-replication"></a>

When you enable default encryption for a replication destination bucket, the following encryption behavior applies:
+ If objects in the source bucket are not encrypted, the replica objects in the destination bucket are encrypted using the default encryption settings of the destination bucket\. This results in the `ETag` of the source object being different from the `ETag` of the replica object\. You must update applications that use the `ETag` to accommodate for this difference\.
+ If objects in the source bucket are encrypted using SSE\-S3 or SSE\-KMS, the replica objects in the destination bucket use the same encryption as the source object encryption\. The default encryption settings of the destination bucket are not used\.

For more information about using default encryption with SSE\-KMS, see [Replicating encrypted objects](replication-config-for-kms-objects.md)\.

## Using Amazon S3 Bucket Keys with default encryption<a name="bucket-key-default-encryption"></a>

When you configure your bucket to use default encryption for SSE\-KMS on new objects, you can also configure S3 Bucket Keys\. S3 Bucket Keys decrease the number of transactions from Amazon S3 to AWS KMS to reduce the cost of server\-side encryption using AWS Key Management Service \(SSE\-KMS\)\. 

When you configure your bucket to use S3 Bucket Keys for SSE\-KMS on new objects, AWS KMS generates a bucket\-level key that is used to create a unique [data key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#data-keys) for objects in the bucket\. This bucket key is used for a time\-limited period within Amazon S3, reducing the need for Amazon S3 to make requests to AWS KMS to complete encryption operations\. 

For more information about using an S3 Bucket Key, see [Using Amazon S3 Bucket Keys](bucket-key.md)\.