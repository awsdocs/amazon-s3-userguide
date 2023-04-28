# Viewing the Object Ownership setting for an S3 bucket<a name="object-ownership-retrieving"></a>

S3 Object Ownership is an Amazon S3 bucket\-level setting that you can use to disable [access control lists \(ACLs\)](acl-overview.md) and take ownership of every object in your bucket, simplifying access management for data stored in Amazon S3\. By default, S3 Object Ownership is set to the bucket owner enforced setting, and ACLs are disabled for new buckets\. With ACLs disabled, the bucket owner owns every object in the bucket and manages access to data exclusively by using access\-management policies\. We recommend that you keep ACLs disabled, except in unusual circumstances where you must control access for each object individually\. 

Object Ownership has three settings that you can use to control ownership of objects uploaded to your bucket and to disable or enable ACLs:

**ACLs disabled**
+ **Bucket owner enforced \(default\)** – ACLs are disabled, and the bucket owner automatically owns and has full control over every object in the bucket\. ACLs no longer affect permissions to data in the S3 bucket\. The bucket uses policies to define access control\.

**ACLs enabled**
+ **Bucket owner preferred** – The bucket owner owns and has full control over new objects that other accounts write to the bucket with the `bucket-owner-full-control` canned ACL\. 
+ **Object writer** – The AWS account that uploads an object owns the object, has full control over it, and can grant other users access to it through ACLs\.

You can view the S3 Object Ownership settings for an Amazon S3 bucket\. To set Object Ownership for a new bucket, see [Setting Object Ownership when you create a bucket](object-ownership-new-bucket.md)\. To set Object Ownership for an existing bucket, see [Setting Object Ownership on an existing bucket](object-ownership-existing-bucket.md)\.

**Permissions:** To use this operation, you must have the `s3:GetBucketOwnershipControls` permission\. For more information about Amazon S3 permissions, see [Actions, resources, and condition keys for Amazon S3](list_amazons3.md)\. 

## Using the S3 console<a name="object-ownership-retrieving-console"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want to apply an Object Ownership setting to\.

1. Choose the **Permissions** tab\.

1. Under **Object Ownership**, you can view the Object Ownership settings for your bucket\.

## Using the AWS CLI<a name="object-ownership-retrieving-cli"></a>

To retrieve the S3 Object Ownership setting for an S3 bucket, use the [get\-bucket\-ownership\-controls](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/get-bucket-ownership-controls.html) AWS CLI command\.

```
aws s3api get-bucket-ownership-controls --bucket DOC-EXAMPLE-BUCKET
```

## Using the REST API<a name="object-ownership-retrieving-rest-api"></a>

To retrieve the Object Ownership setting for an S3 bucket, use the `GetBucketOwnershipControls` API operation\. For more information, see [GetBucketOwnershipControls](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetBucketOwnershipControls.html)\.