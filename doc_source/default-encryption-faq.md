# Amazon S3 now automatically encrypts all new objects<a name="default-encryption-faq"></a>

Amazon S3 now applies server\-side encryption with Amazon S3 managed keys \(SSE\-S3\) as the base level of encryption for every bucket in Amazon S3\. Starting January 5, 2023, all new object uploads to Amazon S3 are automatically encrypted at no additional cost and with no impact on performance\. SSE\-S3, which uses 256\-bit Advanced Encryption Standard \(AES\-256\), is automatically applied to all new buckets and to any existing S3 bucket that doesn't already have default encryption configured\. The automatic encryption status for S3 bucket default encryption configuration and for new object uploads is available in AWS CloudTrail logs, S3 Inventory, S3 Storage Lens, the Amazon S3 console, and as an additional Amazon S3 API response header in the AWS Command Line Interface \(AWS CLI\) and the AWS SDKs\.

The following sections answer questions about this update\. 

**Does Amazon S3 change the default encryption settings for my existing buckets that already have default encryption configured?**  
No\. There are no changes to the default encryption configuration for an existing bucket that already has SSE\-S3 or server\-side encryption with AWS Key Management Service \(AWS KMS\) keys \(SSE\-KMS\) configured\. For more information about how to set the default encryption behavior for buckets, see [Setting default server\-side encryption behavior for Amazon S3 buckets](bucket-encryption.md)\. For more information about SSE\-S3 and SSE\-KMS encryption settings, see [Protecting data using server\-side encryption](serv-side-encryption.md)\.

**Is default encryption enabled on my existing buckets that don't have default encryption configured?**  
Yes\. Amazon S3 now configures default encryption on all existing unencrypted buckets to apply server\-side encryption with S3 managed keys \(SSE\-S3\) as the base level of encryption for new objects uploaded to these buckets\. Objects that are already in an existing unencrypted bucket won't be automatically encrypted\.

**How can I view the default encryption status of new object uploads?**  
Currently, you can view the default encryption status of new object uploads in AWS CloudTrail logs, S3 Inventory, and S3 Storage Lens, the Amazon S3 console, and as an additional Amazon S3 API response header in the AWS Command Line Interface \(AWS CLI\) and the AWS SDKs\.
+ To view your CloudTrail events, see [Viewing CloudTrail events in the CloudTrail console](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events-console.html) in the *AWS CloudTrail User Guide*\. CloudTrail logs provide API tracking for `PUT` and `POST` requests to Amazon S3\. When default encryption is being used to encrypt objects in your buckets, the CloudTrail logs for `PUT` and `POST` API requests will include the following field as the name\-value pair: `"SSEApplied":"Default_SSE_S3"`\. 
+ To view the automatic encryption status of new object uploads in S3 Inventory, configure an S3 Inventory report to include the **Encryption** metadata field, and then see the encryption status of each new object in the report\. For more information, see [Setting up Amazon S3 Inventory](https://docs.aws.amazon.com/AmazonS3/latest/userguide/configure-inventory.html#storage-inventory-setting-up)\.
+ To view the automatic encryption status for new object uploads in S3 Storage Lens, configure an S3 Storage Lens dashboard and see the **Encrypted bytes** and **Encrypted object count** metrics in the **Data protection** category of the dashboard\. For more information, see [Creating an Amazon S3 Storage Lens dashboard](storage_lens_console_creating.md) and [Viewing S3 Storage Lens metrics on the dashboards](storage_lens_view_metrics_dashboard.md)\.
+ To view the automatic bucket\-level encryption status in the Amazon S3 console, check the **Default encryption** of your Amazon S3 buckets in the Amazon S3 console\. For more information, see [Configuring default encryption](default-bucket-encryption.md)\.
+ To view the automatic encryption status as an additional Amazon S3 API response header in the AWS Command Line Interface \(AWS CLI\) and the AWS SDKs, check the response header `x-amz-server-side-encryption` when you use object action APIs, such as [PutObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html) and [GetObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html)\. 

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
HashiCorp Terraform users that aren't using an updated version of the AWS Provider might see an unexpected drift after creating new S3 buckets with no customer defined encryption configuration\. To avoid this drift, update your Terraform AWS Provider version to one of the following versions: any 4\.x release, 3\.76\.1, or 2\.70\.4\.