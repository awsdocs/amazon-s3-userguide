# Managing an S3 on Outposts bucket's lifecycle rules using the console<a name="s3-outposts-bucket-lifecycle"></a>

Lifecycle rules for S3 on Outposts buckets are limited to object deletion and stopping incomplete multipart uploads\. You can use lifecycle rules to define when to initiate object deletion based on age or date\. You can create, enable, disable, delete a lifecycle rule\.

**Note**  
The AWS account that creates the bucket owns it and is the only one can create, enable, disable, delete a lifecycle rule\.

**Topics**
+ [Creating an S3 on Outposts bucket's lifecycle rule](#s3-outposts-bucket-create-lifecycle)
+ [Enabling or disabling an S3 on Outposts bucket's lifecycle rule](#s3-outposts-bucket-enable-lifecycle)
+ [Deleting an S3 on Outposts bucket's lifecycle rule](#s3-outposts-bucket-delete-lifecycle)
+ [Editing an S3 on Outposts bucket's lifecycle rule](#s3-outposts-bucket-edit-lifecycle)

## Creating an S3 on Outposts bucket's lifecycle rule<a name="s3-outposts-bucket-create-lifecycle"></a>

**To create a bucket lifecycle rule**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Outposts buckets**\.

1. Choose the Outposts bucket that you want to create a lifecycle rule for\.

1. Choose the **Management** tab, and then choose **Create Lifecycle rule**\.

1. In the **Lifecycle rule configuration** section:

   1. Enter the **Lifecycle rule name**\.

   1. Choose **Rule scope**\.
**Warning**  
If you want the rule to apply to specific objects, you must use a filter to identify those objects\. Select **Limit the scope to specific objects or tags**\. If selected, do the following:  
Add a prefix filter to limit the scope of this rule to a single prefix\.
Add tags to limit the scope of this rule to the key\-value pairs added below\.

1. In the **lifecycle rule trigger** section, choose the **rule trigger** based on a specific date or object's age\.

## Enabling or disabling an S3 on Outposts bucket's lifecycle rule<a name="s3-outposts-bucket-enable-lifecycle"></a>

**To enable or disable a bucket lifecycle rule**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Outposts buckets**\.

1. Choose the Outposts bucket that you want to enable or disable a lifecycle rule for\.

1. Choose the **Management** tab, and then choose the **Lifecycle rule** that you want to enable or disable\.

1. For **Action**, choose **Enable or disable rule**\.

## Deleting an S3 on Outposts bucket's lifecycle rule<a name="s3-outposts-bucket-delete-lifecycle"></a>

**To delete a bucket lifecycle rule**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Outposts buckets**\.

1. Choose the Outposts bucket that you want to delete a lifecycle rule for\.

1. Choose the **Management** tab, and then choose the **Lifecycle rule** that you want to delete\.

1. Choose **Delete**\.

## Editing an S3 on Outposts bucket's lifecycle rule<a name="s3-outposts-bucket-edit-lifecycle"></a>

**To edit a bucket lifecycle rule**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Outposts buckets**\.

1. Choose the Outposts bucket that you want to edit a lifecycle rule for\.

1. Choose the **Management** tab, and then choose the **Lifecycle rule** that you want to edit\.

1. In the **lifecycle rule configuration** section, do the following:

   1. Update the **lifecycle rule name**\.

   1. Update **rule scope**\.
**Warning**  
If you want the rule to apply to specific objects, you must use a filter to identify those objects\. Choose "Limit the scope to specific objects or tags\."

      If selected, do the following:
      + Add a prefix filter to limit the scope of this rule to a single prefix\.
      + Add tags to limit the scope of this rule to the key\-value pairs added below\.

1. In the **lifecycle rule trigger** section, update the **rule trigger** based on a specific date or object's age\.