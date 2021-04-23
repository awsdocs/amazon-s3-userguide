# Configuring your bucket to use an S3 Bucket Key with SSE\-KMS for new objects<a name="configuring-bucket-key"></a>

When you configure server\-side encryption using SSE\-KMS, you can configure your bucket to use an S3 Bucket Key for SSE\-KMS on new objects\. S3 Bucket Keys decrease the request traffic from Amazon S3 to AWS Key Management Service \(AWS KMS\) and reduce the cost of SSE\-KMS\. For more information, see [Reducing the cost of SSE\-KMS with Amazon S3 Bucket Keys](bucket-key.md)\.

You can configure your bucket to use an S3 Bucket Key for SSE\-KMS on new objects by using the Amazon S3 console, REST API, AWS SDK, AWS CLI, or AWS CloudFormation\. If you want to enable or disable an S3 Bucket Key for existing objects, you can use a COPY operation\. For more information, see [Configuring an S3 Bucket Key at the object level using the REST API, AWS SDKs, or AWS CLI](configuring-bucket-key-object.md)\. 

When an S3 Bucket Key is enabled for the source or destination bucket, the encryption context will be the bucket Amazon Resource Name \(ARN\) and not the object ARN, for example, `arn:aws:s3:::bucket_ARN`\. You need to update your IAM policies to use the bucket ARN for the encryption context\. For more information, see [Granting additional permissions for the IAM role](replication-config-for-kms-objects.md#replication-kms-extra-permissions)\.

The following examples illustrate how an S3 Bucket Key works with replication\. For more information, see [Replicating objects created with server\-side encryption \(SSE\) using AWS KMS CMKs](replication-config-for-kms-objects.md)\. 

**Prerequisite:**  
Before you configure your bucket to use an S3 Bucket Key, review [Changes to note before enabling an S3 Bucket Key](bucket-key.md#bucket-key-changes)\.

**Topics**

## Using the S3 console<a name="enable-bucket-key"></a>

In the S3 console, you can enable or disable an S3 Bucket Key for a new or existing bucket\. Objects in the S3 console inherit their S3 Bucket Key setting from the bucket configuration\. When you enable an S3 Bucket Key for your bucket, new objects that you upload to the bucket use an S3 Bucket Key for server\-side encryption using AWS KMS\. 

**Uploading, copying, or modifying objects in buckets that have an S3 Bucket Key enabled**  
If you upload, modify, or copy an object in a bucket that has an S3 Bucket Key enabled, the S3 Bucket Key settings for that object might be updated to align with bucket configuration\.

If an object already has an S3 Bucket Key enabled, the S3 Bucket Key settings for that object don't change when you copy or modify the object\. However, if you modify or copy an object that doesn’t have an S3 Bucket Key enabled, and the destination bucket has an S3 Bucket Key configuration, the object inherits the destination bucket's S3 Bucket Key settings\. For example, if your source object doesn't have an S3 Bucket Key enabled but the destination bucket has S3 Bucket Key enabled, an S3 Bucket Key will be enabled for the object\.

**To enable an S3 Bucket Key when you create a new bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose **Create bucket**\. 

1. Enter your bucket name, and choose your AWS Region\. 

1. Under **Default encryption**, choose **Enable**\.

1. Under **Encryption type**, choose **AWS Key Management Service key \(SSE\-KMS\)**\.

1. Choose an AWS KMS key:
   + Choose **AWS managed key \(aws/s3\)**\. 
   + Choose **Customer managed key**, and choose a symmetric customer managed CMK in the same Region as your bucket\. 

1. Under **Bucket Key**, choose **Enable**\. 

1. Choose **Create bucket**\. 

   Amazon S3 creates your bucket with an S3 Bucket Key enabled\. New objects that you upload to the bucket will use an S3 Bucket Key\. To disable an S3 Bucket Key, follow the previous steps, and choose **disable**\.

**To enable an S3 Bucket Key for an existing bucket**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the bucket that you want to enable an S3 Bucket Key for\.

1. Choose **Properties**\.

1. Under **Default encryption**, choose **Edit**\.

1. Under **Default encryption**, choose **Enable**\.

1. Under **Encryption type**, choose **AWS Key Management Service key \(SSE\-KMS\)**\.

1. Choose an AWS KMS key:
   + Choose **AWS managed key \(aws/s3\)**\. 
   + Choose **Customer managed key**, and choose a symmetric customer managed CMK in the same Region as your bucket\. 

1. Under **Bucket Key**, choose **Enable**\.

1. Choose **Save changes**\.

   Amazon S3 enables an S3 Bucket Key for new objects added to your bucket\. Existing objects don't use the S3 Bucket Key\. To disable an S3 Bucket Key, follow the previous steps, and choose **Disable**\.

## Using the REST API<a name="enable-bucket-key-rest"></a>

You can use [PutBucketEncryption](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketEncryption.html) to enable or disable an S3 Bucket Key for your bucket\. To configure an S3 Bucket Key with `PutBucketEncryption`, specify the [ServerSideEncryptionRule](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ServerSideEncryptionRule.html), which includes default encryption with server\-side encryption using AWS KMS customer master keys \(CMKs\)\. You can also optionally use a customer managed CMK by specifying the KMS key ID for the CMK\.  

For more information and example syntax, see [PutBucketEncryption](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketEncryption.html)\. 

## Using the AWS SDK Java<a name="enable-bucket-key-sdk"></a>

The following example enables default bucket encryption with SSE\-KMS and an S3 Bucket Key using the AWS SDK for Java\.

------
#### [ Java ]

```
AmazonS3 s3client = AmazonS3ClientBuilder.standard()
    .withRegion(Regions.DEFAULT_REGION)
    .build();
    
ServerSideEncryptionByDefault serverSideEncryptionByDefault = new ServerSideEncryptionByDefault()
    .withSSEAlgorithm(SSEAlgorithm.KMS);
ServerSideEncryptionRule rule = new ServerSideEncryptionRule()
    .withApplyServerSideEncryptionByDefault(serverSideEncryptionByDefault)
    .withBucketKeyEnabled(true);
ServerSideEncryptionConfiguration serverSideEncryptionConfiguration =
    new ServerSideEncryptionConfiguration().withRules(Collections.singleton(rule));

SetBucketEncryptionRequest setBucketEncryptionRequest = new SetBucketEncryptionRequest()
    .withServerSideEncryptionConfiguration(serverSideEncryptionConfiguration)
    .withBucketName(bucketName);
            
s3client.setBucketEncryption(setBucketEncryptionRequest);
```

------

## Using the AWS CLI<a name="enable-bucket-key-cli"></a>

The following example enables default bucket encryption with SSE\-KMS and an S3 Bucket Key using the AWS CLI\.

```
aws s3api put-bucket-encryption --bucket <bucket-name> --server-side-encryption-configuration '{
        "Rules": [
            {
                "ApplyServerSideEncryptionByDefault": {
                    "SSEAlgorithm": "aws:kms",
                    "KMSMasterKeyID": "<KMS-Key-ARN>"
                },
                "BucketKeyEnabled": true
            }
        ]
    }'
```

## Using AWS CloudFormation<a name="enable-bucket-key-cloudformation"></a>

For more information about configuring an S3 Bucket Key using AWS CloudFormation, see [AWS::S3::Bucket ServerSideEncryptionRule](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-s3-bucket-serversideencryptionrule.html) in the *AWS CloudFormation User Guide*\.