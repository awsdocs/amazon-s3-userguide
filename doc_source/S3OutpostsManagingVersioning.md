# Managing S3 Versioning for your S3 on Outposts bucket<a name="S3OutpostsManagingVersioning"></a>

When enabled, S3 Versioning saves multiple distinct copies of an object in the same bucket\. You can use S3 Versioning to preserve, retrieve, and restore every version of every object stored in your Outposts buckets\. S3 Versioning helps you recover from unintended user actions and application failures\. 

Amazon S3 on Outposts buckets have three versioning states:
+ **Unversioned** – If you’ve never enabled or suspended S3 Versioning on your bucket, it is unversioned and returns no S3 Versioning status\. For more information about S3 Versioning, see [Using versioning in S3 buckets](Versioning.md)\.
+ **Enabled** – Enables S3 Versioning for the objects in the bucket\. All objects added to the bucket receive a unique version ID\. Objects that already existed in the bucket at the time that you enable versioning have a version ID of `null`\. If you modify these \(or any other\) objects with other operations, such as [PutObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html), the new objects get a unique version ID\.
+ **Suspended** – Suspends S3 Versioning for the objects in the bucket\. All objects added to the bucket after versioning is suspended receive the version ID `null`\. For more information, see [Adding objects to versioning\-suspended buckets](AddingObjectstoVersionSuspendedBuckets.md)\.

After you enable S3 Versioning for an S3 on Outposts bucket, it can never return to an unversioned state\. However, you can suspend versioning\. For more information about S3 Versioning, see [Using versioning in S3 buckets](Versioning.md)\.

For each object in your bucket, you have a current version and zero or more noncurrent versions\. To reduce storage costs, you can configure your bucket S3 Lifecycle rules to expire noncurrent versions after a specified time period\. For more information, see [Creating and managing a lifecycle configuration for your Amazon S3 on Outposts bucket](S3OutpostsLifecycleManaging.md)\.

The following examples show you how to enable or suspend versioning for an existing S3 on Outposts bucket by using the AWS Management Console and the AWS Command Line Interface \(AWS CLI\)\. To create a bucket with S3 Versioning enabled, see [Creating an S3 on Outposts bucket](S3OutpostsCreateBucket.md)\.

**Note**  
The AWS account that creates the bucket owns it and is the only one that can commit actions to it\. Buckets have configuration properties, such as Outpost, tag, default encryption, and access point settings\. The access point settings include the virtual private cloud \(VPC\), the access point policy for accessing the objects in the bucket, and other metadata\. For more information, see [S3 on Outposts specifications](S3OnOutpostsRestrictionsLimitations.md#S3OnOutpostsSpecifications)\.

## Using the S3 console<a name="S3OutpostsVersioningConsole"></a>

**To edit the S3 Versioning settings for your bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Outposts buckets**\.

1. Choose the Outposts bucket that you want to enable S3 Versioning for\.

1. Choose the **Properties** tab\.

1. Under **Bucket Versioning**, choose **Edit**\.

1. Edit the S3 Versioning settings for the bucket by choosing one of the following options:
   + To suspend S3 Versioning and stop the creation of new object versions, choose **Suspend**\.
   + To enable S3 Versioning and save multiple distinct copies of each object, choose **Enable**\.

1. Choose **Save changes**\.

## Using the AWS CLI<a name="S3OutpostsVersioningCLI"></a>

To enable or suspend S3 Versioning for your bucket by using the AWS CLI, use the `put-bucket-versioning` command, as shown in the following examples\. To use these examples, replace each `user input placeholder` with your own information\. 

For more information, see [put\-bucket\-versioning](https://docs.aws.amazon.com/cli/latest/reference/s3control/put-bucket-versioning.html) in the *AWS CLI Reference*\. 

**Example : To enable S3 Versioning**  

```
aws s3control put-bucket-versioning --account-id 123456789012 --bucket arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/bucket/example-outposts-bucket --versioning-configuration Status=Enabled
```

**Example : To suspend S3 Versioning**  

```
aws s3control put-bucket-versioning --account-id 123456789012 --bucket arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/bucket/example-outposts-bucket --versioning-configuration Status=Suspended
```