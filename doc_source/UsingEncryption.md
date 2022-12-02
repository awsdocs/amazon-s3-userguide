# Protecting data using encryption<a name="UsingEncryption"></a>

Data protection refers to protecting data while in\-transit \(as it travels to and from Amazon S3\) and at rest \(while it is stored on disks in Amazon S3 data centers\)\. You can protect data in transit using Secure Socket Layer/Transport Layer Security \(SSL/TLS\) or client\-side encryption\. You have the following options for protecting data at rest in Amazon S3:
+ **Server\-Side Encryption** – Request Amazon S3 to encrypt your object before saving it on disks in its data centers and then decrypt it when you download the objects\. 

  To configure server\-side encryption, see [Specifying server\-side encryption with AWS KMS \(SSE\-KMS\)](specifying-kms-encryption.md) or [Specifying Amazon S3 encryption](specifying-s3-encryption.md)\.
+ **Client\-Side Encryption** – Encrypt data client\-side and upload the encrypted data to Amazon S3\. In this case, you manage the encryption process, the encryption keys, and related tools\.

  To configure client\-side encryption, see [Protecting data using client\-side encryption](UsingClientSideEncryption.md)\.

To see which percentage of your storage bytes are encrypted, you can use Amazon S3 Storage Lens metrics\. S3 Storage Lens is a cloud\-storage analytics feature that you can use to gain organization\-wide visibility into object\-storage usage and activity\. For more information, see [ Assessing your storage activity and usage with S3 Storage Lens](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage_lens?icmpid=docs_s3_user_guide_UsingEncryption.html)\. For a complete list of metrics, see [ S3 Storage Lens metrics glossary](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage_lens_metrics_glossary.html?icmpid=docs_s3_user_guide_UsingEncryption)\.

For more information about server\-side encryption and client\-side encryption, review the following topics\.

**Topics**
+ [Protecting data using server\-side encryption](serv-side-encryption.md)
+ [Protecting data using client\-side encryption](UsingClientSideEncryption.md)