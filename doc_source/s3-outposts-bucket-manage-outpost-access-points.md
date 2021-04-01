--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Managing your Amazon S3 on Outposts access points with Amazon S3 console<a name="s3-outposts-bucket-manage-outpost-access-points"></a>

**Topics**
+ [Create your S3 on Outposts bucket's access points](#s3-outposts-bucket-create-accesspoint)
+ [Managing your S3 on Outposts access point's policy](#s3-outposts-edit-accesspoint)
+ [Deleting your S3 on Outposts access point](#s3-outposts-bucket-delete-policy)

## Creating your Amazon S3 on Outposts bucket's access points with Amazon S3 console<a name="s3-outposts-bucket-create-accesspoint"></a>

**To create an S3 on Outposts access point:**

1. Sign in to the [Amazon S3 console](https://console.aws.amazon.com/s3)\.

1. Choose **Outposts access points** from the left navigation menu\.

1. Choose **Create Outposts access points** from the left navigation menu\.

1. In the section **Outposts access point settings**, enter a **Access point name** for the access point and choose the Virtual Private \(VPC\)\.
**Note**  
To use an access point with a VPC, you must modify the access policy for your VPC endpoint\. For more information, see [Accessing Amazon S3 on Outposts using virtual private cloud \(VPC\) only access points](AccessingS3Outposts.md)\.

1. If you want to add a policy for your access point, you can do so by entering it in the **Outposts access point policy** section\. For more information, see [Using AWS Identity and Access Management with Amazon S3 on Outposts](S3OutpostsIAM.md)\.

## Managing your Amazon S3 on Outposts access point's policy with Amazon S3 console<a name="s3-outposts-edit-accesspoint"></a>

**To add or edit an S3 on Outposts access point's policy:**

1. Sign in to the [Amazon S3 console](https://console.aws.amazon.com/s3)\.

1. Choose **Outposts access points** from the left navigation menu\.

1. Choose the Outposts access points that you want to edit the access point policy for\.

1. In the section, **Outposts access points**, select the access point whose policy you want to edit and choose **Edit policy**\.

1. You can then add or edit the S3 on Outposts access point policy by entering it in the **Outposts access point policy**section\. For more information, see [Using AWS Identity and Access Management with Amazon S3 on Outposts](S3OutpostsIAM.md)\.

## Deleting your Amazon S3 on Outposts access point with Amazon S3 console<a name="s3-outposts-bucket-delete-policy"></a>

**To delete an S3 on Outposts access point:**

1. Sign in to the [Amazon S3 console](https://console.aws.amazon.com/s3)\.

1. Choose **Outposts access points** from the left navigation menu\.

1. In the section **Outposts access points**, choose the Outposts access point that you want to delete\.

1. Choose **Delete**\.

1. Confirm the deletion\.