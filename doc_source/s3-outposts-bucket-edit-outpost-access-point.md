# Managing your Amazon S3 on Outposts bucket access points with Amazon S3 console<a name="s3-outposts-bucket-edit-outpost-access-point"></a>

**Note**  
The AWS account that creates the Outposts bucket owns it and is the only one can assign access points to it\.

**Topics**
+ [Create your S3 on Outposts bucket's access points](#s3-outposts-bucket-create-accesspoint)
+ [Managing your S3 on Outposts bucket's access point's policy](#s3-outposts-bucket-edit-accesspoint)
+ [Deleting your S3 on Outposts access point](#s3-outposts-bucket-delete-policy)

## Creating your Amazon S3 on Outposts bucket's access points with Amazon S3 console<a name="s3-outposts-bucket-create-accesspoint"></a>

**To create an S3 on Outposts access point:**

1. Sign in to the [Amazon S3 console](https://console.aws.amazon.com/s3)\.

1. Choose **Outposts buckets** from the left navigation menu\.

1. Choose the Outposts bucket that you want to create an Outposts access point for\.

1. Choose the **Outposts access points** tab\.

1. In the section, **Outposts access points**, choose **Create Outposts access point**\.

1. In the section, **Outposts access point settings**, enter a **access point name** for the access point and choose the Virtual Private \(VPC\)\.
**Note**  
To use an access point with a VPC, you must modify the access policy for your VPC endpoint\. For more information, see [Accessing Amazon S3 on Outposts using VPC\-only access points](WorkingWithS3Outposts.md#AccessingS3Outposts)\.

1. If you want to add a policy for your access point, you can do so by entering it in the **Outposts access point policy** section\. For more information, see [Using AWS Identity and Access Management with Amazon S3 on Outposts](S3OutpostsIAM.md)\.

## Managing your Amazon S3 on Outposts bucket's access point's policy with Amazon S3 console<a name="s3-outposts-bucket-edit-accesspoint"></a>

**To add or edit an S3 on Outposts access point:**

1. Sign in to the [Amazon S3 console](https://console.aws.amazon.com/s3)\.

1. Choose **Outposts buckets** from the left navigation menu\.

1. Choose the Outposts bucket that you want to edit the access point policy for\.

1. Choose the **Outposts access points** tab\.

1. In the section, **Outposts access points**, select the access point whose policy you want to edit and choose **Edit policy**\.

1. You can then add or edit the S3 on Outposts access point policy by entering it in the **Outposts access point policy**section\. For more information, see [Using AWS Identity and Access Management with Amazon S3 on Outposts](S3OutpostsIAM.md)\.

## Deleting your Amazon S3 on Outposts access point with Amazon S3 console<a name="s3-outposts-bucket-delete-policy"></a>

**To delete an S3 on Outposts access point:**

1. Sign in to the [Amazon S3 console](https://console.aws.amazon.com/s3)\.

1. Choose **Outposts buckets** from the left navigation menu\.

1. Choose the **Outposts access points** tab\.

1. In the section **Outposts access points**, choose the Outposts access point that you want to delete\.

1. Choose **Delete**\.

1. Confirm the deletion\.