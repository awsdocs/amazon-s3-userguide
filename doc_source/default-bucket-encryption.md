# Enabling Amazon S3 default bucket encryption<a name="default-bucket-encryption"></a>

You can set the default encryption behavior on an Amazon S3 bucket so that all objects are encrypted when they are stored in the bucket\. The objects are encrypted using server\-side encryption with either Amazon S3\-managed keys \(SSE\-S3\) or AWS Key Management Service \(AWS KMS\) keys\.

When you configure default encryption using AWS KMS, you can also configure S3 Bucket Key\. For more information, see [Reducing the cost of SSE\-KMS with Amazon S3 Bucket Keys](bucket-key.md)\.

Default encryption works with all existing and new Amazon S3 buckets\. Without default encryption, to encrypt all objects stored in a bucket, you must include encryption information with every object storage request\. You must also set up an Amazon S3 bucket policy to reject storage requests that don't include encryption information\. 

There are no additional charges for using default encryption for S3 buckets\. Requests to configure the default encryption feature incur standard Amazon S3 request charges\. For information about pricing, see [Amazon S3 pricing](https://aws.amazon.com/s3/pricing/)\. For SSE\-KMS KMS key storage, AWS KMS charges apply and are listed at [AWS KMS pricing](https://aws.amazon.com/kms/pricing/)\. 

**Changes to note before enabling default encryption**  
After you enable default encryption for a bucket, the following encryption behavior applies:
+ There is no change to the encryption of the objects that existed in the bucket before default encryption was enabled\. 
+ When you upload objects after enabling default encryption:
  + If your `PUT` request headers don't include encryption information, Amazon S3 uses the bucket’s default encryption settings to encrypt the objects\. 
  + If your `PUT` request headers include encryption information, Amazon S3 uses the encryption information from the `PUT` request to encrypt objects before storing them in Amazon S3\.
+ If you use the SSE\-KMS option for your default encryption configuration, you are subject to the RPS \(requests per second\) limits of AWS KMS\. For more information about AWS KMS limits and how to request a limit increase, see [AWS KMS limits](https://docs.aws.amazon.com/kms/latest/developerguide/limits.html)\. 

## Using the S3 console<a name="bucket-encryption-how-to-set-up-console"></a>

**To enable default encryption on an Amazon S3 bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want\. 

1. Choose **Properties**\.

1. Under **Default encryption**, choose **Edit**\.

1. To enable or disable server\-side encryption, choose **Enable** or **Disable**\.

1. To enable server\-side encryption using an Amazon S3\-managed key, under **Encryption key type**, choose **Amazon S3 key \(SSE\-S3\)**\.

   For more information about using Amazon S3 server\-side encryption to encrypt your data, see [Using server\-side encryption with Amazon S3\-managed encryption keys \(SSE\-S3\)](UsingServerSideEncryption.md)\.

1. To enable server\-side encryption using an AWS KMS key, follow these steps:

   1. Under **Encryption key type**, choose **AWS Key Management Service key \(SSE\-KMS\)**\.
**Important**  
If you use the AWS KMS option for your default encryption configuration, you are subject to the RPS \(requests per second\) limits of AWS KMS\. For more information about AWS KMS quotas and how to request a quota increase, see [Quotas](https://docs.aws.amazon.com/kms/latest/developerguide/limits.html)\. 

   1. Under **AWS KMS key** choose one of the following:
      + **AWS managed key \(aws/s3\)**
      + **Choose from your KMS root keys**, and choose your **KMS root key**\.
      + **Enter KMS root key ARN**, and enter your AWS KMS key ARN\.
**Important**  
You can only use KMS keys that are enabled in the same AWS Region as the bucket\. When you choose **Choose from your KMS keys**, the S3 console only lists 100 KMS keys per Region\. If you have more than 100 KMS keys in the same Region, you can only see the first 100 KMS keys in the S3 console\. To use a KMS key that is not listed in the console, choose **Custom KMS ARN**, and enter the KMS key ARN\.  
When you use an AWS KMS key for server\-side encryption in Amazon S3, you must choose a symmetric encryption KMS key\. Amazon S3 only supports symmetric encryption KMS keys and not asymmetric KMS keys\. For more information, see [Using symmetric and asymmetric keys](https://docs.aws.amazon.com/kms/latest/developerguide/symmetric-asymmetric.html) in the *AWS Key Management Service Developer Guide*\.

      For more information about creating an AWS KMS key, see [Creating keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\. For more information about using AWS KMS with Amazon S3, see [Using server\-side encryption with AWS Key Management Service \(SSE\-KMS\)](UsingKMSEncryption.md)\.

1. To use S3 Bucket Keys, under **Bucket Key**, choose **Enable**\.

   When you configure your bucket to use default encryption with SSE\-KMS, you can also enable S3 Bucket Key\. S3 Bucket Keys decrease request traffic from Amazon S3 to AWS KMS and lower the cost of encryption\. For more information, see [Reducing the cost of SSE\-KMS with Amazon S3 Bucket Keys](bucket-key.md)\.

1. Choose **Save changes**\.

## Using the AWS CLI<a name="default-bucket-encryption-cli"></a>

These examples show you how to configure default encryption using Amazon S3\-managed encryption \(SSE\-S3\) or AWS KMS encryption \(SSE\-KMS\) with an S3 Bucket Key\.

For more information about default encryption, see [Setting default server\-side encryption behavior for Amazon S3 buckets](bucket-encryption.md)\. For more information about using the AWS CLI to configure default encryption, see [put\-bucket\-encryption](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/put-bucket-encryption.html)\.

**Example – Default encryption with SSE\-S3**  
This example configures default bucket encryption with Amazon S3\-managed encryption\.  

```
aws s3api put-bucket-encryption --bucket bucket-name --server-side-encryption-configuration '{
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
aws s3api put-bucket-encryption --bucket bucket-name --server-side-encryption-configuration '{
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

Use the REST API PUT Bucket encryption operation to enable default encryption and to set the type of server\-side encryption to use—SSE\-S3 or SSE\-KMS\. 

For more information, see [PutBucketEncryption](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTencryption.html) in the *Amazon Simple Storage Service API Reference*\.