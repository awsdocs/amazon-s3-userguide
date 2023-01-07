# Amazon S3 now automatically encrypts all new objects<a name="default-encryption-faq"></a>

Amazon S3 now applies server\-side encryption with Amazon S3 managed keys \(SSE\-S3\) as the base level of encryption for every bucket in Amazon S3\. Starting January 5, 2023, all new object uploads to Amazon S3 will be automatically encrypted at no additional cost and with no impact on performance\. SSE\-S3, which uses 256\-bit Advanced Encryption Standard \(AES\-256\), is automatically applied to all new buckets and to any existing S3 bucket that doesn't already have default encryption configured\. Currently, the automatic encryption status for S3 bucket default encryption configuration and for new object uploads is available in AWS CloudTrail logs\. During the next few weeks, the automatic encryption status will also be rolled out to the Amazon S3 console, S3 Inventory, S3 Storage Lens, and as an additional Amazon S3 API response header in the AWS Command Line Interface \(AWS CLI\) and the AWS SDKs\. This process is currently occurring across all AWS Regions\. When this update is complete in all Regions, we will update the documentation\.

The following sections answer questions about this update\. 

**How can I view the automatic encryption status today?**  
Starting January 5, 2023, the automatic encryption status for S3 bucket default encryption configuration and all new object uploads is visible in AWS CloudTrail logs across all AWS Regions, including the AWS GovCloud \(US\) Regions and the AWS China Regions\. Over the next few weeks, we will roll out this automatic encryption status to the Amazon S3 console, S3 Inventory, S3 Storage Lens, and Amazon S3 API responses in the AWS CLI and AWS SDKs in all Regions\.

**How will I know that this update is available in the Amazon S3 console, S3 Inventory, S3 Storage Lens, and Amazon S3 API responses in the AWS CLI and AWS SDKs?**  
After this update is fully rolled out, we will update the Amazon S3 documentation to indicate that this change is now available in the Amazon S3 console, S3 Inventory, S3 Storage Lens, and as an additional Amazon S3 API response header in the AWS CLI and the AWS SDKs\.

**Will Amazon S3 change the default encryption settings for my existing buckets that already have default encryption configured?**  
No\. There will be no changes to the default encryption configuration for an existing bucket that already has SSE\-S3 or server\-side encryption with AWS Key Management Service \(AWS KMS\) keys \(SSE\-KMS\) configured\. For more information about how to set the default encryption behavior for buckets, see [Setting default server\-side encryption behavior for Amazon S3 buckets](bucket-encryption.md)\. For more information about SSE\-S3 and SSE\-KMS encryption settings, see [Protecting data using server\-side encryption](serv-side-encryption.md)\.

**Is Amazon S3 encrypting my existing buckets that are unencrypted?**  
Yes\. Amazon S3 now configures default encryption on all existing unencrypted buckets to apply server\-side encryption with S3 managed keys \(SSE\-S3\) as the base level of encryption\. This change in encryption status will be rolled out over the next few weeks\.

**How can I view the default encryption status of new object uploads?**  
Currently, you can view the default encryption status of new object uploads in AWS CloudTrail logs\. To view your CloudTrail events, see [Viewing CloudTrail events in the CloudTrail console](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events-console.html) in the *AWS CloudTrail User Guide*\. CloudTrail logs provide API tracking for `PUT` and `POST` requests to Amazon S3\. When default encryption is being used to encrypt objects in your buckets, the CloudTrail logs for `PUT` and `POST` API requests will include the following field as the name\-value pair: `"SSEApplied":"Default_SSE_S3"`\. Over the next few weeks, the automatic encryption status will also be rolled out to the bucket level encryption status in the Amazon S3 console, S3 Inventory, S3 Storage Lens, and as an additional Amazon S3 API response header in the AWS CLI and the AWS SDKs\. 

**What do I have to do to take advantage of this change?**  
You are not required to make any changes to your existing applications\. Because default encryption is enabled for all of your buckets, all new objects uploaded to Amazon S3 are automatically encrypted\.

**Can I disable encryption for the new objects being written to my bucket?**  
No\. SSE\-S3 is the new base level of encryption that's applied to all the new objects being uploaded to your bucket\. You can no longer disable encryption for new object uploads\.

**Will my charges be affected?**  
No\. Default encryption with SSE\-S3 is available at no additional cost\. You will be billed for storage, requests, and other S3 features, as usual\. For pricing, see [Amazon S3 pricing](http://aws.amazon.com/s3/pricing/)\.

**Will Amazon S3 encrypt my existing objects that are unencrypted?**  
No\. Beginning on January 5, 2023, Amazon S3 only automatically encrypts new object uploads\. To encrypt existing objects, you can use S3 Batch Operations to create encrypted copies of your objects\. These encrypted copies will retain the existing object data and name and will be encrypted by using the encryption keys that you specify\. For more details, see [Encrypting objects with Amazon S3 Batch Operations](http://aws.amazon.com/blogs/storage/encrypting-objects-with-amazon-s3-batch-operations/) in the *AWS Storage Blog*\.

**I did not enable encryption for my buckets before this release\. Do I need to change the way that I access objects?**  
No\. Default encryption with SSE\-S3 automatically encrypts your data as it's written to Amazon S3 and decrypts it for you when you access it\. There is no change in the way that you access objects that are automatically encrypted\.

**Do I need to change the way that I access my client\-side encrypted objects?**  
No\. All client\-side encrypted objects that are encrypted before being uploaded into Amazon S3 arrive as encrypted ciphertext objects within Amazon S3\. These objects will now have an additional layer of SSE\-S3 encryption\. Your workloads that use client\-side encrypted objects will not require any changes to your client services or authorization settings\.

**Note**  
HashiCorp Terraform users that aren't using an updated version of the AWS Provider might see an unexpected drift after creating new S3 buckets with no customer defined encryption configuration\. To avoid this drift with the new automatic default encryption for your existing unencrypted and new S3 buckets, update your Terraform AWS Provider version to one of the following versions: 4\.49\.0, 3\.76\.1, or 2\.70\.2\.