# Protecting data using encryption<a name="UsingEncryption"></a>

**Important**  
Amazon S3 now applies server\-side encryption with Amazon S3 managed keys \(SSE\-S3\) as the base level of encryption for every bucket in Amazon S3\. Starting January 5, 2023, all new object uploads to Amazon S3 are automatically encrypted at no additional cost and with no impact on performance\. The automatic encryption status for S3 bucket default encryption configuration and for new object uploads is available in AWS CloudTrail logs, S3 Inventory, S3 Storage Lens, the Amazon S3 console, and as an additional Amazon S3 API response header in the AWS Command Line Interface and AWS SDKs\. For more information, see [Default encryption FAQ](https://docs.aws.amazon.com/AmazonS3/latest/userguide/default-encryption-faq.html)\.

Data protection refers to protecting data while in\-transit \(as it travels to and from Amazon S3\) and at rest \(while it is stored on disks in Amazon S3 data centers\)\. You can protect data in transit by using Secure Socket Layer/Transport Layer Security \(SSL/TLS\) or client\-side encryption\. For protecting data at rest in Amazon S3, you have the following options:
+ **Server\-side encryption** – Amazon S3 encrypts your objects before saving them on disks in AWS data centers and then decrypts the objects when you download them\.

  All Amazon S3 buckets have encryption configured by default and all new objects uploaded to an S3 bucket are automatically encrypted at rest\. Server\-side encryption with Amazon S3 managed keys \(SSE\-S3\) is the base level of encryption configuration for every bucket in Amazon S3\. If you want to use a different type of default encryption, you can also specify server\-side encryption with AWS Key Management Service \(AWS KMS\) keys \(SSE\-KMS\) or customer\-provided keys \(SSE\-C\) in your S3 `PUT` requests or set the default encryption configuration in the destination bucket to use SSE\-KMS to encrypt your data\.

  To configure server\-side encryption, see [Specifying server\-side encryption with AWS KMS \(SSE\-KMS\)](specifying-kms-encryption.md), [Specifying Amazon S3 encryption with S3 managed keys \(SSE\-S3\)](specifying-s3-encryption.md), or [Specifying server\-side encryption with customer\-provided keys \(SSE\-C\)](ServerSideEncryptionCustomerKeys.md#specifying-s3-c-encryption)\.
+ **Client\-side encryption** – You encrypt your data client\-side and upload the encrypted data to Amazon S3\. In this case, you manage the encryption process, encryption keys, and related tools\.

  To configure client\-side encryption, see [Protecting data using client\-side encryption](UsingClientSideEncryption.md)\.

To see which percentage of your storage bytes are encrypted, you can use Amazon S3 Storage Lens metrics\. S3 Storage Lens is a cloud\-storage analytics feature that you can use to gain organization\-wide visibility into object\-storage usage and activity\. For more information, see [ Assessing your storage activity and usage with S3 Storage Lens](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage_lens?icmpid=docs_s3_user_guide_UsingEncryption.html)\. For a complete list of metrics, see [ S3 Storage Lens metrics glossary](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage_lens_metrics_glossary.html?icmpid=docs_s3_user_guide_UsingEncryption)\.

For more information about server\-side encryption and client\-side encryption, review the following topics\.

**Topics**
+ [Protecting data using server\-side encryption](serv-side-encryption.md)
+ [Protecting data using client\-side encryption](UsingClientSideEncryption.md)