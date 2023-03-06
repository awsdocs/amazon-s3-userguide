# Configuring default encryption<a name="default-bucket-encryption"></a>

**Important**  
Amazon S3 now applies server\-side encryption with Amazon S3 managed keys \(SSE\-S3\) as the base level of encryption for every bucket in Amazon S3\. Starting January 5, 2023, all new object uploads to Amazon S3 are automatically encrypted at no additional cost and with no impact on performance\. The automatic encryption status for S3 bucket default encryption configuration and for new object uploads is available in AWS CloudTrail logs, S3 Inventory, S3 Storage Lens, the Amazon S3 console, and as an additional Amazon S3 API response header in the AWS Command Line Interface and AWS SDKs\. For more information, see [Default encryption FAQ](https://docs.aws.amazon.com/AmazonS3/latest/userguide/default-encryption-faq.html)\.

Amazon S3 buckets have bucket encryption enabled by default, and new objects are automatically encrypted by using server\-side encryption with Amazon S3 managed keys \(SSE\-S3\)\. This encryption applies to all new objects in your Amazon S3 buckets, and comes at no cost to you\.

If you need more control over your encryption keys, such as managing key rotation and access policy grants, you can elect to use the server\-side encryption with AWS Key Management Service \(AWS KMS\) keys \(SSE\-KMS\)\. For more information about SSE\-KMS, see [Specifying server\-side encryption with AWS KMS \(SSE\-KMS\)](specifying-kms-encryption.md)\. If you want to use a KMS key that is owned by a different account, you must have permission to use the key\. For more information about cross\-account permissions for KMS keys, see [Creating KMS keys that other accounts can use](https://docs.aws.amazon.com/kms/latest/developerguide/key-policy-modifying-external-accounts.html#cross-account-console) in the *AWS Key Management Service Developer Guide*\. 

When you set the default bucket encryption to SSE\-KMS, you can also configure an S3 Bucket Key to reduce your AWS KMS request costs\. For more information, see [Reducing the cost of SSE\-KMS with Amazon S3 Bucket Keys](bucket-key.md)\.

There are no additional charges for using default encryption for S3 buckets\. Requests to configure the default encryption behavior incur standard Amazon S3 request charges\. For information about pricing, see [Amazon S3 pricing](https://aws.amazon.com/s3/pricing/)\. For SSE\-KMS, AWS KMS charges apply and are listed at [AWS KMS pricing](https://aws.amazon.com/kms/pricing/)\. 

Server\-side encryption with customer\-provided keys \(SSE\-C\) is not supported for default encryption\.

You can configure Amazon S3 default encryption for an S3 bucket by using the Amazon S3 console, the AWS SDKs, the Amazon S3 REST API, and the AWS Command Line Interface \(AWS CLI\)\.

**Changes to note before enabling default encryption**  
After you enable default encryption for a bucket, the following encryption behavior applies:
+ There is no change to the encryption of the objects that existed in the bucket before default encryption was enabled\. 
+ When you upload objects after enabling default encryption:
  + If your `PUT` request headers don't include encryption information, Amazon S3 uses the bucket’s default encryption settings to encrypt the objects\. 
  + If your `PUT` request headers include encryption information, Amazon S3 uses the encryption information from the `PUT` request to encrypt objects before storing them in Amazon S3\.
+ If you use the SSE\-KMS option for your default encryption configuration, you are subject to the requests per second \(RPS\) quotas of AWS KMS\. For more information about AWS KMS quotas and how to request a quota increase, see [Quotas](https://docs.aws.amazon.com/kms/latest/developerguide/limits.html) in the *AWS Key Management Service Developer Guide*\. 

**Note**  
Objects uploaded before default encryption was enabled will not be encrypted\. For information about encrypting existing objects, see [Setting default server\-side encryption behavior for Amazon S3 buckets](bucket-encryption.md)\.

## Using the S3 console<a name="bucket-encryption-how-to-set-up-console"></a>

**To configure default encryption on an Amazon S3 bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. In the **Buckets** list, choose the name of the bucket that you want\. 

1. Choose the **Properties** tab\.

1. Under **Default encryption**, choose **Edit**\.

1. To configure default encryption, under **Encryption key type**, choose one of the following: 
   + **Amazon S3 managed key \(SSE\-S3\)**
   + **AWS Key Management Service key \(SSE\-KMS\)**
**Important**  
If you use the AWS KMS option for your default encryption configuration, you are subject to the requests per second \(RPS\) quotas of AWS KMS\. For more information about AWS KMS quotas and how to request a quota increase, see [Quotas](https://docs.aws.amazon.com/kms/latest/developerguide/limits.html) in the *AWS Key Management Service Developer Guide*\. 

   Buckets and new objects are encrypted by default with SSE\-S3\. For more information about default encryption, see [Setting default server\-side encryption behavior for Amazon S3 buckets](bucket-encryption.md)\.

   For more information about using Amazon S3 server\-side encryption to encrypt your data, see [Protecting data using server\-side encryption with Amazon S3 managed encryption keys \(SSE\-S3\)](UsingServerSideEncryption.md)\.

1. If you chose **AWS Key Management Service key \(SSE\-KMS\)**, do the following: 

   1. Under **AWS KMS key**, specify your KMS key in one of the following ways:
      + To choose from a list of available KMS keys, choose **Choose from your AWS KMS keys**, and choose your **KMS key** from the list of available keys\.

        Both the AWS managed key \(`aws/s3`\) and your customer managed keys appear in this list\. For more information about customer managed keys, see [Customer keys and AWS keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#key-mgmt) in the *AWS Key Management Service Developer Guide*\.
      + To enter the KMS key ARN, choose **Enter AWS KMS key ARN**, and enter your KMS key ARN in the field that appears\. 
      + To create a new customer managed key in the AWS KMS console, choose **Create a KMS key**\.

        For more information about creating an AWS KMS key, see [Creating keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\.
**Important**  
You can only use KMS keys that are enabled in the same AWS Region as the bucket\. When you choose **Choose from your KMS keys**, the S3 console only lists 100 KMS keys per Region\. If you have more than 100 KMS keys in the same Region, you can only see the first 100 KMS keys in the S3 console\. To use a KMS key that is not listed in the console, choose **Custom KMS ARN**, and enter the KMS key ARN\.  
When you use an AWS KMS key for server\-side encryption in Amazon S3, you must choose a symmetric encryption KMS key\. Amazon S3 only supports symmetric encryption KMS keys\. For more information about these keys, see [Symmetric encryption KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#symmetric-cmks) in the *AWS Key Management Service Developer Guide*\.

      For more information about using SSE\-KMS with Amazon S3, see [Protecting data using server\-side encryption with AWS Key Management Service keys \(SSE\-KMS\)](UsingKMSEncryption.md)\.

   1. When you configure your bucket to use default encryption with SSE\-KMS, you can also enable an S3 Bucket Key\. S3 Bucket Keys lower the cost of encryption by decreasing request traffic from Amazon S3 to AWS KMS\. For more information, see [Reducing the cost of SSE\-KMS with Amazon S3 Bucket Keys](bucket-key.md)\.

      To use S3 Bucket Keys, under **Bucket Key**, choose **Enable**\.

1. Choose **Save changes**\.

## Using the AWS CLI<a name="default-bucket-encryption-cli"></a>

These examples show you how to configure default encryption by using SSE\-S3 or by using SSE\-KMS with an S3 Bucket Key\.

For more information about default encryption, see [Setting default server\-side encryption behavior for Amazon S3 buckets](bucket-encryption.md)\. For more information about using the AWS CLI to configure default encryption, see [put\-bucket\-encryption](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/put-bucket-encryption.html)\.

**Example – Default encryption with SSE\-S3**  
This example configures default bucket encryption with Amazon S3 managed keys\.  

```
aws s3api put-bucket-encryption --bucket DOC-EXAMPLE-BUCKET --server-side-encryption-configuration '{
    "Rules": [
        {
            "ApplyServerSideEncryptionByDefault": {
                "SSEAlgorithm": "AES256"
            }
        }
    ]
}'
```

**Example – Default encryption with SSE\-KMS using an S3 Bucket Key**  
This example configures default bucket encryption with SSE\-KMS using an S3 Bucket Key\.   

```
aws s3api put-bucket-encryption --bucket DOC-EXAMPLE-BUCKET --server-side-encryption-configuration '{
    "Rules": [
            {
                "ApplyServerSideEncryptionByDefault": {
                    "SSEAlgorithm": "aws:kms",
                    "KMSMasterKeyID": "KMS-Key-ARN"
                },
                "BucketKeyEnabled": true
            }
        ]
    }'
```

## Using the REST API<a name="bucket-encryption-how-to-set-up-api"></a>

Use the REST API `PutBucketEncryption` operation to enable default encryption and to set the type of server\-side encryption to use—SSE\-S3 or SSE\-KMS\. 

For more information, see [PutBucketEncryption](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTencryption.html) in the *Amazon Simple Storage Service API Reference*\.