# Setting Object Ownership when you create a bucket<a name="object-ownership-new-bucket"></a>

When you create a bucket, you can configure S3 Object Ownership\. To set Object Ownership for an existing bucket, see [Setting Object Ownership on an existing bucket](object-ownership-existing-bucket.md)\.

S3 Object Ownership is an Amazon S3 bucket\-level setting that you can use to disable [access control lists \(ACLs\)](acl-overview.md) and take ownership of every object in your bucket, simplifying access management for data stored in Amazon S3\. We recommend that you disable ACLs unless you to need to control access at the individual object level\. 

Object Ownership has three settings that you can use to control ownership of objects uploaded to your bucket and to disable or enable ACLs:

**ACLs disabled**
+ **Bucket owner enforced \(recommended\)** – ACLs are disabled, and the bucket owner automatically owns and has full control over every object in the bucket\. ACLs no longer affect permissions to data in the S3 bucket\. The bucket uses policies to define access control\.

**ACLs enabled**
+ **Bucket owner preferred** – The bucket owner owns and has full control over new objects that other accounts write to the bucket with the `bucket-owner-full-control` canned ACL\. 
+ **Object writer \(default\)** – The AWS account that uploads an object owns the object, has full control over it, and can grant other users access to it through ACLs\.

**Permissions**: To create a bucket and select a setting for Object Ownership, you must have both the `s3:CreateBucket` and `s3:PutBucketOwnershipControls` permissions\. For more information about Amazon S3 permissions, see [Actions, resources, and condition keys for Amazon S3](list_amazons3.md)\. 

## Using the S3 console<a name="object-ownership-new-bucket-console"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose **Create bucket**\.

   The **Create bucket** wizard opens\.

1. In **Bucket name**, enter a DNS\-compliant name for your bucket\.

   The bucket name must:
   + Be unique across all of Amazon S3\.
   + Be between 3 and 63 characters long\.
   + Not contain uppercase characters\.
   + Start with a lowercase letter or number\.

   After you create the bucket, you cannot change its name\. For information about naming buckets, see [Bucket naming rules](bucketnamingrules.md)\. 
**Important**  
Avoid including sensitive information, such as account number, in the bucket name\. The bucket name is visible in the URLs that point to the objects in the bucket\.

1. In **Region**, choose the AWS Region where you want the bucket to reside\. 

   Choose a Region close to you to minimize latency and costs and address regulatory requirements\. Objects stored in a Region never leave that Region unless you explicitly transfer them to another Region\. For a list of Amazon S3 AWS Regions, see [AWS service endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#s3_region) in the *Amazon Web Services General Reference*\.

1. Under **Object Ownership**, to disable or enable ACLs and control ownership of objects uploaded in your bucket, choose one of the following settings:

**ACLs disabled**
   + **Bucket owner enforced** – ACLs are disabled, and the bucket owner automatically owns and has full control over every object in the bucket\. ACLs no longer affect permissions to data in the S3 bucket\. The bucket uses policies to define access control\.

     To require that all new buckets are created with ACLs disabled by using IAM or AWS Organizations policies, see [Disabling ACLs for all new buckets \(bucket owner enforced\)](ensure-object-ownership.md#object-ownership-requiring-bucket-owner-enforced)\.

**ACLs enabled**
   + **Bucket owner preferred** – The bucket owner owns and has full control over new objects that other accounts write to the bucket with the `bucket-owner-full-control` canned ACL\. 

     If you apply the bucket owner preferred setting, to require all Amazon S3 uploads to include the `bucket-owner-full-control` canned ACL, you can [add a bucket policy](ensure-object-ownership.md#ensure-object-ownership-bucket-policy) that only allows object uploads that use this ACL\.
   + **Object writer** – The AWS account that uploads an object owns the object, has full control over it, and can grant other users access to it through ACLs\.
**Note**  
To apply the **Bucket owner enforced** setting or the **Bucket owner preferred** setting, you must have the following permission: `s3:CreateBucket` and `s3:PutBucketOwnershipControls`\.

1. In **Bucket settings for Block Public Access**, choose the Block Public Access settings that you want to apply to the bucket\. 

   We recommend that you keep all settings enabled unless you know that you need to turn off one or more of them for your use case, such as to host a public website\. Block Public Access settings that you enable for the bucket are also enabled for all access points that you create on the bucket\. For more information about blocking public access, see [Blocking public access to your Amazon S3 storage](access-control-block-public-access.md)\.

1. \(Optional\) If you want to enable S3 Object Lock, do the following:

   1. Choose **Advanced settings**\.
**Important**  
You can only enable S3 Object Lock for a bucket when you create it\. If you enable Object Lock for the bucket, you cannot disable it later\. Enabling Object Lock also enables versioning for the bucket\. After you enable Object Lock for the bucket, you must configure the Object Lock default retention and legal hold settings to protect new objects from being deleted or overwritten\. For more information, see [Configuring S3 Object Lock using the console](object-lock-console.md)\.

   1. If you want to enable Object Lock, choose **Enable**, read the warning that appears, and acknowledge it\.

   For more information about the S3 Object Lock feature, see [Using S3 Object Lock](object-lock.md)\.
**Note**  
To create an Object Lock enabled bucket, you must have the following permissions: s3:CreateBucket, s3:PutBucketVersioning and s3:PutBucketObjectLockConfiguration\.

1. Choose **Create bucket**\.

## Using the AWS CLI<a name="object-ownership-new-bucket-cli"></a>

To set Object Ownership when you create a new bucket, use the `create-bucket` AWS CLI command with the `--object-ownership` parameter\. 

This example applies the bucket owner enforced setting for a new bucket using the AWS CLI:

```
aws s3api create-bucket --bucket  DOC-EXAMPLE-BUCKET --region us-east-1 --object-ownership BucketOwnerEnforced
```

## Using the AWS SDK for Java<a name="object-ownership-new-bucket-sdk-java"></a>

This example sets the bucket owner enforced setting for a new bucket using the AWS SDK for Java:

```
    // Build the ObjectOwnership for CreateBucket
    CreateBucketRequest createBucketRequest = CreateBucketRequest.builder()
            .bucket(bucketName)
            .objectOwnership(ObjectOwnership.BucketOwnerEnforced)
            .build()

     // Send the request to S3 
     s3client.createBucket(createBucketRequest);
```

## Using AWS CloudFormation<a name="object-ownership-new-bucket-cfn"></a>

To use the `AWS::S3::Bucket` AWS CloudFormation resource to set Object Ownership when you create a new bucket, see [OwnershipControls within AWS::S3::Bucket](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-s3-bucket.html#cfn-s3-bucket-ownershipcontrols) in the *AWS CloudFormation User Guide*\.

## Using the REST API<a name="object-ownership-new-bucket-rest-api"></a>

To apply the bucket owner enforced setting for S3 Object Ownership, use the `CreateBucket` API operation with the `x-amz-object-ownership` request header set to `BucketOwnerEnforced`\. For information and examples, see [CreateBucket](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CreateBucket.html) in the *Amazon Simple Storage Service API Reference*\.

**Next steps**: After you apply the bucket owner enforced or bucket owner preferred settings for Object Ownership, you can further take the following steps:
+ [Bucket owner enforced](ensure-object-ownership.md#object-ownership-requiring-bucket-owner-enforced) – Require that all new buckets are created with ACLs disabled by using an IAM or Organizations policy\. 
+ [Bucket owner preferred](ensure-object-ownership.md#ensure-object-ownership-bucket-policy) – Add an S3 bucket policy to require the `bucket-owner-full-control` canned ACL for all object uploads to your bucket\.