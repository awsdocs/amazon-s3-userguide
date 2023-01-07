# Protecting data using server\-side encryption<a name="serv-side-encryption"></a>

**Important**  
Amazon S3 now applies server\-side encryption with Amazon S3 managed keys \(SSE\-S3\) as the base level of encryption for every bucket in Amazon S3\. Starting January 5, 2023, all new object uploads to Amazon S3 will be automatically encrypted at no additional cost and with no impact on performance\. Currently, the automatic encryption status for S3 bucket default encryption configuration and for new object uploads is available in AWS CloudTrail logs\. During the next few weeks, the automatic encryption status will also be rolled out to the Amazon S3 console, S3 Inventory, S3 Storage Lens, and as an additional Amazon S3 API response header in the AWS Command Line Interface and AWS SDKs\. When this update is complete in all AWS Regions, we will update the documentation\. For more information, see [Default encryption FAQ](https://docs.aws.amazon.com/AmazonS3/latest/userguide/default-encryption-faq.html)\.

Server\-side encryption is the encryption of data at its destination by the application or service that receives it\. Amazon S3 encrypts your data at the object level as it writes it to disks in its data centers and decrypts it for you when you access it\. As long as you authenticate your request and you have access permissions, there is no difference in the way you access encrypted or unencrypted objects\. For example, if you share your objects using a presigned URL, that URL works the same way for both encrypted and unencrypted objects\. Additionally, when you list objects in your bucket, the list API returns a list of all objects, regardless of whether they are encrypted\.

**Note**  
You can't apply different types of server\-side encryption to the same object simultaneously\.

You have three mutually exclusive options, depending on how you choose to manage the encryption keys\.

**Server\-side encryption with Amazon S3 managed keys \(SSE\-S3\)**  
When you use server\-side encryption with Amazon S3 managed keys \(SSE\-S3\), each object is encrypted with a unique key\. As an additional safeguard, it encrypts the key itself with a root key that it regularly rotates\. Amazon S3 server\-side encryption uses one of the strongest block ciphers available, 256\-bit Advanced Encryption Standard \(AES\-256\) GCM, to encrypt your data\. For objects encrypted prior to AES\-GCM, AES\-CBC is still supported to decrypt those objects\. For more information, see [Using server\-side encryption with Amazon S3\-managed encryption keys \(SSE\-S3\)](UsingServerSideEncryption.md)\.

**Server\-side encryption with KMS keys stored in AWS Key Management Service \(SSE\-KMS\)**  
Server\-side encryption with AWS KMS keys \(SSE\-KMS\) is similar to SSE\-S3, but with some additional benefits and charges for using this service\. There are separate permissions for the use of a KMS key that provides added protection against unauthorized access of your objects in Amazon S3\. SSE\-KMS also provides you with an audit trail that shows when your KMS key was used and by whom\. Additionally, you can create and manage customer managed keys or use AWS managed keys that are unique to you, your service, and your Region\. For more information, see [Using server\-side encryption with AWS Key Management Service \(SSE\-KMS\)](UsingKMSEncryption.md)\.

**Server\-side encryption with customer\-provided keys \(SSE\-C\)**  
With server\-side encryption with customer\-provided keys \(SSE\-C\), you manage the encryption keys and Amazon S3 manages the encryption, as it writes to disks, and decryption, when you access your objects\. For more information, see [Using server\-side encryption with customer\-provided keys \(SSE\-C\)](ServerSideEncryptionCustomerKeys.md)\.