# Setting Object Ownership when you create a bucket<a name="object-ownership-new-bucket"></a>

When you create a bucket, you can configure S3 Object Ownership\. To set Object Ownership for an existing bucket, see [Setting Object Ownership on an existing bucket](object-ownership-existing-bucket.md)\.

S3 Object Ownership is an Amazon S3 bucket\-level setting that you can use to disable [access control lists \(ACLs\)](acl-overview.md) and take ownership of every object in your bucket, simplifying access management for data stored in Amazon S3\. We recommend that you disable ACLs unless you to need to control access at the individual object level\. 

Object Ownership has three settings that you can use to control ownership of objects uploaded to your bucket and to disable or enable ACLs:

**ACLs disabled**
+ **Bucket owner enforced \(recommended\)** – ACLs are disabled, and the bucket owner automatically owns and has full control over every object in the bucket\. ACLs no longer affect permissions to data in the S3 bucket\. The bucket uses policies to define access control\.

**ACLs enabled**
+ **Bucket owner preferred** – The bucket owner owns and has full control over new objects that other accounts write to the bucket with the `bucket-owner-full-control` canned ACL\. 
+ **Object writer \(default\)** – The AWS account that uploads an object owns the object, has full control over it, and can grant other users access to it through ACLs\.

**Permissions**: To apply the **Bucket owner enforced** setting or the **Bucket owner preferred** setting, you must have the following permissions: `s3:CreateBucket` and `s3:PutBucketOwnershipControls`\. No additional permissions are needed when creating a bucket with the **Object writer** setting applied\. For more information about Amazon S3 permissions, see [Actions, resources, and condition keys for Amazon S3](list_amazons3.md)\. 

**Important**  
A majority of modern use cases in Amazon S3 no longer require the use of ACLs, and we recommend that you disable ACLs except in unusual circumstances where you need to control access for each object individually\. With Object Ownership, you can disable ACLs and rely on policies for access control\. When you disable ACLs, you can easily maintain a bucket with objects uploaded by different AWS accounts\. You, as the bucket owner, own all the objects in the bucket and can manage access to them using policies\. 

## Using the S3 console<a name="object-ownership-new-bucket-console"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. Choose **Create bucket**\.

   The **Create bucket** page opens\.

1. For **Bucket name**, enter a name for your bucket\.

   The bucket name must:
   + Be unique within a partition\. A partition is a grouping of Regions\. AWS currently has three partitions: `aws` \(Standard Regions\), `aws-cn` \(China Regions\), and `aws-us-gov` \(AWS GovCloud \[US\] Regions\)\.
   + Be between 3 and 63 characters long\.
   + Consist only of lowercase letters, numbers, dots \(\.\), and hyphens \(\-\)\. For best compatibility, we recommend that you avoid using dots \(\.\) in bucket names, except for buckets that are used only for static website hosting\.
   + Begin and end with a letter or number\.

   After you create the bucket, you cannot change its name\. For more information about naming buckets, see [Bucket naming rules](bucketnamingrules.md)\. 
**Important**  
Avoid including sensitive information, such as account numbers, in the bucket name\. The bucket name is visible in the URLs that point to the objects in the bucket\.

1. For **Region**, choose the AWS Region where you want the bucket to reside\. 

   To minimize latency and costs and address regulatory requirements, choose a Region close to you\. Objects stored in a Region never leave that Region unless you explicitly transfer them to another Region\. For a list of Amazon S3 AWS Regions, see [AWS service endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#s3_region) in the *Amazon Web Services General Reference*\.

1. Under **Object Ownership**, to disable or enable ACLs and control ownership of objects uploaded in your bucket, choose one of the following settings:

**ACLs disabled**
   + **Bucket owner enforced** – ACLs are disabled, and the bucket owner automatically owns and has full control over every object in the bucket\. ACLs no longer affect access permissions to data in the S3 bucket\. The bucket uses policies to define access control\.

     To require that all new buckets are created with ACLs disabled by using AWS Identity and Access Management \(IAM\) or AWS Organizations policies, see [Disabling ACLs for all new buckets \(bucket owner enforced\)](ensure-object-ownership.md#object-ownership-requiring-bucket-owner-enforced)\.

**ACLs enabled**
   + **Bucket owner preferred** – The bucket owner owns and has full control over new objects that other accounts write to the bucket with the `bucket-owner-full-control` canned ACL\. 

     If you apply the bucket owner preferred setting, to require all Amazon S3 uploads to include the `bucket-owner-full-control` canned ACL, you can [add a bucket policy](ensure-object-ownership.md#ensure-object-ownership-bucket-policy) that allows only object uploads that use this ACL\.
   + **Object writer** – The AWS account that uploads an object owns the object, has full control over it, and can grant other users access to it through ACLs\.
**Note**  
To apply the **Bucket owner enforced** setting or the **Bucket owner preferred** setting, you must have permission to use the following actions: `s3:CreateBucket` and `s3:PutBucketOwnershipControls`\.

1. Under **Block Public Access settings for this bucket**, choose the Block Public Access settings that you want to apply to the bucket\. 

   We recommend that you keep all settings enabled unless you know that you need to turn off one or more of them for your use case, such as to host a public website\. The Block Public Access settings that you enable for the bucket are also enabled for all access points that you create on the bucket\. For more information about blocking public access, see [Blocking public access to your Amazon S3 storage](access-control-block-public-access.md)\.

1. \(Optional\) Under **Bucket Versioning**, you can choose if you wish to keep variants of objects in your bucket\. For more information about versioning, see [Using versioning in S3 buckets](Versioning.md)\.

   To disable or enable versioning on your bucket, choose either **Disable** or **Enable**\.

1. \(Optional\) Under **Tags**, you can choose to add tags to your bucket\. Tags are key\-value pairs used to categorize storage\.

   To add a bucket tag, enter a **Key** and optionally a **Value** and choose **Add Tag**\.

1. Under **Default encryption**, choose **Edit**\.

1. To configure default encryption, under **Encryption key type**, choose one of the following: 
   + **Amazon S3 managed key \(SSE\-S3\)**
   + **AWS Key Management Service key \(SSE\-KMS\)**
**Important**  
If you use the SSE\-KMS option for your default encryption configuration, you are subject to the requests per second \(RPS\) quota of AWS KMS\. For more information about AWS KMS quotas and how to request a quota increase, see [Quotas](https://docs.aws.amazon.com/kms/latest/developerguide/limits.html) in the *AWS Key Management Service Developer Guide*\.

   Buckets and new objects are encrypted with server\-side encryption with an **Amazon S3 managed key** as the base level of encryption configuration\. For more information about default encryption, see [Setting default server\-side encryption behavior for Amazon S3 buckets](bucket-encryption.md)\.

   For more information about using Amazon S3 server\-side encryption to encrypt your data, see [Protecting data using server\-side encryption with Amazon S3 managed encryption keys \(SSE\-S3\)](UsingServerSideEncryption.md)\.

1. If you chose **AWS Key Management Service key \(SSE\-KMS\)**, do the following:

   1. Under **AWS KMS key**, specify your KMS key in one of the following ways:
      + To choose from a list of available KMS keys, choose **Choose from your AWS KMS keys**, and choose your **KMS key** from the list of available keys\.

        Both the AWS managed key \(`aws/s3`\) and your customer managed keys appear in this list\. For more information about customer managed keys, see [Customer keys and AWS keys](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#key-mgmt) in the *AWS Key Management Service Developer Guide*\.
      + To enter the KMS key ARN, choose **Enter AWS KMS key ARN**, and enter your KMS key ARN in the field that appears\. 
      + To create a new customer managed key in the AWS KMS console, choose **Create a KMS key**\.

        For more information about creating an AWS KMS key, see [Creating keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\.
**Important**  
You can use only KMS keys that are available in the same AWS Region as the bucket\. The Amazon S3 console lists only the first 100 KMS keys in the same Region as the bucket\. To use a KMS key that is not listed, you must enter your KMS key ARN\. If you want to use a KMS key that is owned by a different account, you must first have permission to use the key and then you must enter the KMS key ARN\. For more information on cross account permissions for KMS keys, see [Creating KMS keys that other accounts can use](https://docs.aws.amazon.com/kms/latest/developerguide/key-policy-modifying-external-accounts.html#cross-account-console) in the *AWS Key Management Service Developer Guide*\. For more information on SSE\-KMS, see [Specifying server\-side encryption with AWS KMS \(SSE\-KMS\)](specifying-kms-encryption.md)\.  
When you use an AWS KMS key for server\-side encryption in Amazon S3, you must choose a symmetric encryption KMS key\. Amazon S3 supports only symmetric encryption KMS keys and not asymmetric KMS keys\. For more information, see [Identifying symmetric and asymmetric KMS keys](https://docs.aws.amazon.com/kms/latest/developerguide/find-symm-asymm.html) in the *AWS Key Management Service Developer Guide*\.

      For more information about creating an AWS KMS key, see [Creating keys](https://docs.aws.amazon.com/kms/latest/developerguide/create-keys.html) in the *AWS Key Management Service Developer Guide*\. For more information about using AWS KMS with Amazon S3, see [Protecting data using server\-side encryption with AWS Key Management Service keys \(SSE\-KMS\)](UsingKMSEncryption.md)\.

   1. When you configure your bucket to use default encryption with SSE\-KMS, you can also enable S3 Bucket Keys\. S3 Bucket Keys lower the cost of encryption by decreasing request traffic from Amazon S3 to AWS KMS\. For more information, see [Reducing the cost of SSE\-KMS with Amazon S3 Bucket Keys](bucket-key.md)\.

      To use S3 Bucket Keys, under **Bucket Key**, choose **Enable**\.

1. \(Optional\) If you want to enable S3 Object Lock, do the following:

   \(Optional\) If you want to enable S3 Object Lock, do the following:

   1. Choose **Advanced settings**\.
**Important**  
You can only enable Object Lock for a bucket when you create it, and you cannot disable it later\. Enabling Object Lock also enables versioning for the bucket\. After enabling you must configure the Object Lock default retention and legal hold settings to protect new objects from being deleted or overwritten\.

   1. If you want to enable Object Lock, choose **Enable**, read the warning that appears, and acknowledge it\.

   For more information, see [Using S3 Object Lock](object-lock.md)\.
**Note**  
To create an Object Lock enabled bucket, you must have the following permissions: `s3:CreateBucket`, `s3:PutBucketVersioning` and `s3:PutBucketObjectLockConfiguration`\.

1. Choose **Create bucket**\.

## Using the AWS CLI<a name="object-ownership-new-bucket-cli"></a>

To set Object Ownership when you create a new bucket, use the `create-bucket` AWS CLI command with the `--object-ownership` parameter\. 

This example applies the bucket owner enforced setting for a new bucket using the AWS CLI:

```
aws s3api create-bucket --bucket  DOC-EXAMPLE-BUCKET --region us-east-1 --object-ownership BucketOwnerEnforced
```

**Important**  
If you don’t set Object Ownership when you create a bucket using the CLI, the default setting will be ObjectWriter \(ACLs enabled\)\.

## Using the AWS SDK for Java<a name="object-ownership-new-bucket-sdk-java"></a>

This example sets the bucket owner enforced setting for a new bucket using the AWS SDK for Java:

```
    // Build the ObjectOwnership for CreateBucket
    CreateBucketRequest createBucketRequest = CreateBucketRequest.builder()
            .bucket(bucketName)
            .objectOwnership(ObjectOwnership.BucketOwnerEnforced)
            .build()

     // Send the request to Amazon S3 
     s3client.createBucket(createBucketRequest);
```

## Using AWS CloudFormation<a name="object-ownership-new-bucket-cfn"></a>

To use the `AWS::S3::Bucket` AWS CloudFormation resource to set Object Ownership when you create a new bucket, see [OwnershipControls within AWS::S3::Bucket](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-s3-bucket.html#cfn-s3-bucket-ownershipcontrols) in the *AWS CloudFormation User Guide*\.

## Using the REST API<a name="object-ownership-new-bucket-rest-api"></a>

To apply the bucket owner enforced setting for S3 Object Ownership, use the `CreateBucket` API operation with the `x-amz-object-ownership` request header set to `BucketOwnerEnforced`\. For information and examples, see [CreateBucket](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CreateBucket.html) in the *Amazon Simple Storage Service API Reference*\.

**Next steps**: After you apply the bucket owner enforced or bucket owner preferred settings for Object Ownership, you can further take the following steps:
+ [Bucket owner enforced](ensure-object-ownership.md#object-ownership-requiring-bucket-owner-enforced) – Require that all new buckets are created with ACLs disabled by using an IAM or Organizations policy\. 
+ [Bucket owner preferred](ensure-object-ownership.md#ensure-object-ownership-bucket-policy) – Add an S3 bucket policy to require the `bucket-owner-full-control` canned ACL for all object uploads to your bucket\.