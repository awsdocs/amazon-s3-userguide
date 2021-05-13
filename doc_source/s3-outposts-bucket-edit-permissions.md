# Managing Amazon S3 on Outposts bucket permissions using the console<a name="s3-outposts-bucket-edit-permissions"></a>

You can use the Amazon S3 console to manage your S3 on Outposts bucket permissions, including creating, editing, and deleting bucket policies\.

**Note**  
The AWS account that creates the Outposts bucket owns it and is the only one can change bucket permissions\.

**Topics**
+ [Creating or editing an S3 on Outposts bucket policy](#s3-outposts-bucket-edit-policy)
+ [Deleting an S3 on Outposts bucket policy](#s3-outposts-bucket-delete-policy)

## Creating or editing an S3 on Outposts bucket policy<a name="s3-outposts-bucket-edit-policy"></a>

**To create or edit a bucket policy**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Outposts buckets**\.

1. Choose the Outposts bucket whose permission you want to edit\.

1. Choose the **Permissions** tab\.

1. In the **Outposts bucket policy** section, do one of the following:
   + To create a new policy, choose **Edit**\.
   + To edit a bucket policy, choose the policy that you want to edit, and then choose **Edit**\.

   You can then add or edit the S3 on Outposts bucket policy\. For more information, see [Using AWS Identity and Access Management with Amazon S3 on Outposts](S3OutpostsIAM.md)\.

## Deleting an S3 on Outposts bucket policy<a name="s3-outposts-bucket-delete-policy"></a>

**To delete a bucket policy**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Outposts buckets**\.

1. Choose the Outposts bucket whose permission you want to edit\.

1. Choose the **Permissions** tab\.

1. In the **Outposts bucket policy** section, choose **Delete**\.

1. Confirm the deletion\.