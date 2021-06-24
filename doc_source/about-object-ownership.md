# Controlling ownership of uploaded objects using S3 Object Ownership<a name="about-object-ownership"></a>

 S3 Object Ownership is an Amazon S3 bucket setting that you can use to control ownership of new objects that are uploaded to your buckets\. By default, when other AWS accounts upload objects to your bucket, the objects remain owned by the uploading account\. With S3 Object Ownership, any new objects that are written by other accounts with the `bucket-owner-full-control` canned access control list \(ACL\) automatically become owned by the bucket owner, who then has full control of the objects\. 

You can create shared data stores that multiple users and teams in different accounts can write to and read from, and standardize ownership of new objects in your bucket\. As the bucket owner, you can then share and manage access to these objects via resource\-based policies, such as a bucket policy\. S3 Object Ownership does not affect existing objects\. 

S3 Object Ownership has two settings:
+ **Object writer** – The uploading account will own the object\.
+ **Bucket owner preferred** – The bucket owner will own the object if the object is uploaded with the `bucket-owner-full-control` canned ACL\. Without this setting and canned ACL, the object is uploaded and remains owned by the uploading account\. For information about enforcing object ownership, see [How do I ensure that I take ownership of new objects?](#ensure-object-ownership)\. 

**Topics**
+ [How do I ensure that I take ownership of new objects?](#ensure-object-ownership)
+ [Using S3 Object Ownership with Amazon S3 Replication](#object-ownership-replication)
+ [Setting S3 Object Ownership](#enable-object-ownership)

## How do I ensure that I take ownership of new objects?<a name="ensure-object-ownership"></a>

After setting S3 Object Ownership to *bucket owner preferred*, you can add a bucket policy to require all Amazon S3 PUT operations to include the `bucket-owner-full-control` canned ACL\. This ACL grants the bucket owner full control of new objects\. With the S3 Object Ownership setting, it transfers object ownership to the bucket owner\. If the uploader doesn't meet the ACL requirement in their upload, the request fails\. This enables bucket owners to enforce uniform object ownership across all newly uploaded objects in their buckets\.

The following bucket policy specifies that account *`111122223333`* can upload objects to *`DOC-EXAMPLE-BUCKET`* only when the object's ACL is set to `bucket-owner-full-control`\. Be sure to replace *`111122223333`* with a real account and *`DOC-EXAMPLE-BUCKET`* with the name of a real bucket\.

```
{
   "Version": "2012-10-17",
   "Statement": [
      {
         "Sid": "Only allow writes to my bucket with bucket owner full control",
         "Effect": "Allow",
         "Principal": {
            "AWS": [
               "arn:aws:iam::111122223333:user/ExampleUser"
            ]
         },
         "Action": [
            "s3:PutObject",
            "s3:PutObjectAcl"
         ],
         "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*",
         "Condition": {
            "StringEquals": {
               "s3:x-amz-acl": "bucket-owner-full-control"
            }
         }
      }
   ]
}
```

The following is an example copy operation that includes the `bucket-owner-full-control` canned ACL using the AWS Command Line Interface \(AWS CLI\)\.

```
aws s3 cp file.txt s3://DOC-EXAMPLE-BUCKET --acl bucket-owner-full-control
```

If the client does not include the `bucket-owner-full-control` canned ACL, the operation fails, and the uploader receives the following error: 

An error occurred \(AccessDenied\) when calling the PutObject operation: Access Denied\.

**Note**  
If clients need access to objects after uploading, you must grant additional permissions to the uploading account\. For information about granting accounts access to your resources, see [Example walkthroughs: Managing access to your Amazon S3 resources ](example-walkthroughs-managing-access.md)\.

## Using S3 Object Ownership with Amazon S3 Replication<a name="object-ownership-replication"></a>

S3 Object Ownership does not change the behavior of Amazon S3 Replication\. In replication, the owner of the source object also owns the replica by default\. When the source and destination buckets are owned by different AWS accounts, you can add optional configuration settings to change replica ownership\. 

To transfer ownership of replicated objects to the destination bucket owner, you can use the Amazon S3 Replication owner override option\. For more information about transferring ownership of replicas, see [Changing the replica owner](replication-change-owner.md)\.

## Setting S3 Object Ownership<a name="enable-object-ownership"></a>

This section provides examples of how to enable S3 Object Ownership\. You can use the AWS Management Console, which provides a UI to manage permissions without writing any code\. 

### Setting S3 Object Ownership to bucket owner preferred in the AWS Management Console<a name="add-object-ownership"></a>

S3 Object Ownership enables you to take ownership of new objects that other AWS accounts upload to your bucket with the `bucket-owner-full-control` canned access control list \(ACL\)\. This section describes how to set Object Ownership using the console\.

**Setting Object Ownership to bucket owner preferred on an S3 bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want to enable S3 Object Ownership for\.

1. Choose the **Permissions** tab\.

1. Choose **Edit** under **Object Ownership**\.

1. Choose **Bucket owner preferred**, and then choose **Save**\.

With the above steps Object Ownership will take ownership of any new objects that are written by other accounts with the `bucket-owner-full-control` canned ACL\. For more information about enforcing Object Ownership, see [How do I ensure that I take ownership of new objects?](#ensure-object-ownership)\.
