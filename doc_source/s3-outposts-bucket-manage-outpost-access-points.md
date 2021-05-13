# Managing Amazon S3 on Outposts access points using the console<a name="s3-outposts-bucket-manage-outpost-access-points"></a>

You can use the Amazon S3 console to manage your S3 on Outposts access points, including creating and deleting access points and managing policies\.

**Topics**
+ [Creating an S3 on Outposts bucket's access points](#s3-outposts-bucket-create-accesspoint)
+ [Managing an S3 on Outposts access point's policy](#s3-outposts-edit-accesspoint)
+ [Deleting an S3 on Outposts access point](#s3-outposts-bucket-delete-policy)

## Creating an S3 on Outposts bucket's access points<a name="s3-outposts-bucket-create-accesspoint"></a>

**To create an S3 on Outposts bucket's access point**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Outposts access points**\.

1. Choose **Create Outposts access points**\.

1. In the **Outposts access point settings** section, enter a name for the access point, and choose the virtual private cloud \(VPC\)\.
**Note**  
To use an access point with a VPC, you must modify the access policy for your VPC endpoint\. For more information, see [Accessing Amazon S3 on Outposts using VPC\-only access points](WorkingWithS3Outposts.md#AccessingS3Outposts)\.

1. To add a policy for your access point, enter it in the **Outposts access point policy** section\. For more information, see [Using AWS Identity and Access Management with Amazon S3 on Outposts](S3OutpostsIAM.md)\.

## Managing an S3 on Outposts access point's policy<a name="s3-outposts-edit-accesspoint"></a>

**To add or edit an S3 on Outposts access point's policy**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Outposts access points**\.

1. Choose the Outposts access points that you want to edit the access point policy for\.

1. In the **Outposts access points** section, choose the access point whose policy you want to edit, and then choose **Edit policy**\.

1. Add or edit the policy in the **Outposts access point policy** section\. For more information, see [Using AWS Identity and Access Management with Amazon S3 on Outposts](S3OutpostsIAM.md)\.

## Deleting an S3 on Outposts access point<a name="s3-outposts-bucket-delete-policy"></a>

**To delete an access point**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Outposts access points**\.

1. In the **Outposts access points** section, choose the Outposts access point that you want to delete\.

1. Choose **Delete**\.

1. Confirm the deletion\.