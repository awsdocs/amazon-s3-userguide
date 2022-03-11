# Creating and managing a lifecycle rule using the AWS Management Console<a name="S3OutpostsLifecycleConsole"></a>

Lifecycle rules for S3 on Outposts buckets are limited to object deletion\. You can use lifecycle rules to define when to initiate object deletion based on age or date\. You can create, enable, disable, or delete a lifecycle rule\.

**Note**  
The AWS account that creates the bucket owns it and is the only one that can create, enable, disable, or delete a lifecycle rule\.

To create and manage a lifecycle rule for an S3 on Outposts using the AWS Management Console, see the following topics\.

**Topics**
+ [Creating a lifecycle rule](#s3-outposts-bucket-create-lifecycle)
+ [Enabling a lifecycle rule](#s3-outposts-bucket-enable-lifecycle)
+ [Editing a lifecycle rule](#s3-outposts-bucket-edit-lifecycle)
+ [Deleting a lifecycle rule](#s3-outposts-bucket-delete-lifecycle)

## Creating a lifecycle rule<a name="s3-outposts-bucket-create-lifecycle"></a>

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

## Enabling a lifecycle rule<a name="s3-outposts-bucket-enable-lifecycle"></a>

**To enable or disable a bucket lifecycle rule**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Outposts buckets**\.

1. Choose the Outposts bucket that you want to enable or disable a lifecycle rule for\.

1. Choose the **Management** tab, and then choose the **Lifecycle rule** that you want to enable or disable\.

1. For **Action**, choose **Enable or disable rule**\.

## Editing a lifecycle rule<a name="s3-outposts-bucket-edit-lifecycle"></a>

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

## Deleting a lifecycle rule<a name="s3-outposts-bucket-delete-lifecycle"></a>

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Outposts buckets**\.

1. Choose the Outposts bucket that you want to delete a lifecycle rule for\.

1. Choose the **Management** tab, and then choose the **Lifecycle rule** that you want to delete\.

1. Choose **Delete**\.