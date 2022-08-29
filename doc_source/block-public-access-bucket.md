# Using the S3 console<a name="block-public-access-bucket"></a>

Amazon S3 Block Public Access prevents the application of any settings that allow public access to data within S3 buckets\. This section describes how to edit Block Public Access settings for one or more S3 buckets\. For information about blocking public access using the AWS CLI, AWS SDKs, and the Amazon S3 REST APIs, see [Blocking public access to your Amazon S3 storage](access-control-block-public-access.md)\.

You can see if your bucket is publicly accessible in the **Buckets** list\. In the **Access** column, Amazon S3 labels the permissions for a bucket as follows:
+ **Public** – Everyone has access to one or more of the following: List objects, Write objects, Read and write permissions\. 
+ **Objects can be public** – The bucket is not public, but anyone with the appropriate permissions can grant public access to objects\. 
+ **Buckets and objects not public** – The bucket and objects do not have any public access\.
+ **Only authorized users of this account** – Access is isolated to IAM users and roles in this account and AWS service principals because there is a policy that grants public access\.

You can also filter bucket searches by access type\. Choose an access type from the drop\-down list that is next to the **Search for buckets** bar\. 

If you see an `Error` when you list your buckets and their public access settings, you might not have the required permissions\. Check to make sure you have the following permissions added to your user or role policy:

```
s3:GetAccountPublicAccessBlock
s3:GetBucketPublicAccessBlock
s3:GetBucketPolicyStatus
s3:GetBucketLocation
s3:GetBucketAcl
s3:ListAccessPoints
s3:ListAllMyBuckets
```

In some rare cases, requests can also fail because of an AWS Region outage\.

**To edit the Amazon S3 block public access settings for a single S3 bucket**

Follow these steps if you need to change the public access settings for a single S3 bucket\.

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Bucket name** list, choose the name of the bucket that you want\.

1. Choose **Permissions**\.

1. Choose **Edit** to change the public access settings for the bucket\. For more information about the four Amazon S3 Block Public Access Settings, see [Block public access settings](access-control-block-public-access.md#access-control-block-public-access-options)\.

1. Choose the setting that you want to change, and then choose **Save**\.

1. When you're asked for confirmation, enter **confirm**\. Then choose **Confirm** to save your changes\.

You can change Amazon S3 Block Public Access settings when you create a bucket\. For more information, see [Creating a bucket](create-bucket-overview.md)\. 