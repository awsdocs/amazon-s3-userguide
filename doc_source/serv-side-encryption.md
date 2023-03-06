# Protecting data using server\-side encryption<a name="serv-side-encryption"></a>

**Important**  
Amazon S3 now applies server\-side encryption with Amazon S3 managed keys \(SSE\-S3\) as the base level of encryption for every bucket in Amazon S3\. Starting January 5, 2023, all new object uploads to Amazon S3 are automatically encrypted at no additional cost and with no impact on performance\. The automatic encryption status for S3 bucket default encryption configuration and for new object uploads is available in AWS CloudTrail logs, S3 Inventory, S3 Storage Lens, the Amazon S3 console, and as an additional Amazon S3 API response header in the AWS Command Line Interface and AWS SDKs\. For more information, see [Default encryption FAQ](https://docs.aws.amazon.com/AmazonS3/latest/userguide/default-encryption-faq.html)\.

Server\-side encryption is the encryption of data at its destination by the application or service that receives it\. Amazon S3 encrypts your data at the object level as it writes it to disks in AWS data centers and decrypts it for you when you access it\. As long as you authenticate your request and you have access permissions, there is no difference in the way you access encrypted or unencrypted objects\. For example, if you share your objects by using a presigned URL, that URL works the same way for both encrypted and unencrypted objects\. Additionally, when you list objects in your bucket, the list API operations return a list of all objects, regardless of whether they are encrypted\.

All Amazon S3 buckets have encryption configured by default and all new objects uploaded to an S3 bucket are automatically encrypted at rest\. Server\-side encryption with Amazon S3 managed keys \(SSE\-S3\) is the base level of encryption configuration for every bucket in Amazon S3\. If you want to use a different type of default encryption, you can also specify server\-side encryption with AWS Key Management Service \(AWS KMS\) keys \(SSE\-KMS\) or customer\-provided keys \(SSE\-C\) in your S3 `PUT` requests or set the default encryption configuration in the destination bucket to use SSE\-KMS to encrypt your data\.

**Note**  
You can't apply different types of server\-side encryption to the same object simultaneously\.

If you need to encrypt your existing objects, use S3 Batch Operations and S3 Inventory\. For more information, see [ Encrypting objects with Amazon S3 Batch Operations ](http://aws.amazon.com/blogs/storage/encrypting-objects-with-amazon-s3-batch-operations/) and [Performing large\-scale batch operations on Amazon S3 objects](batch-ops.md)\.

You have three mutually exclusive options for server\-side encryption, depending on how you choose to manage the encryption keys\.

**Server\-side encryption with Amazon S3 managed keys \(SSE\-S3\)**  
All Amazon S3 buckets have encryption configured by default\. The default option for server\-side encryption is with Amazon S3 managed keys \(SSE\-S3\)\. Each object is encrypted with a unique key\. As an additional safeguard, SSE\-S3 encrypts the key itself with a root key that it regularly rotates\. SSE\-S3 uses one of the strongest block ciphers available, 256\-bit Advanced Encryption Standard \(AES\-256\), to encrypt your data\. For more information, see [Protecting data using server\-side encryption with Amazon S3 managed encryption keys \(SSE\-S3\)](UsingServerSideEncryption.md)\.

**Server\-side encryption with AWS Key Management Service \(AWS KMS\) keys \(SSE\-KMS\)**  
Server\-side encryption with AWS KMS keys \(SSE\-KMS\) is provided through an integration of the AWS KMS service with Amazon S3\. With AWS KMS, you have more control over your keys\. For example, you can view separate keys, edit control policies, and follow the keys in AWS CloudTrail\. Additionally, you can create and manage customer managed keys or use AWS managed keys that are unique to you, your service, and your Region\. For more information, see [Protecting data using server\-side encryption with AWS Key Management Service keys \(SSE\-KMS\)](UsingKMSEncryption.md)\.

**Server\-side encryption with customer\-provided keys \(SSE\-C\)**  
With server\-side encryption with customer\-provided keys \(SSE\-C\), you manage the encryption keys, and Amazon S3 manages the encryption as it writes to disks, and the decryption when you access your objects\. For more information, see [Using server\-side encryption with customer\-provided keys \(SSE\-C\)](ServerSideEncryptionCustomerKeys.md)\.