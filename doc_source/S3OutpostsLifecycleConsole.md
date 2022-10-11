# Creating and managing a lifecycle rule by using the AWS Management Console<a name="S3OutpostsLifecycleConsole"></a>

Lifecycle rules for Amazon S3 on Outposts buckets are limited to object deletion\. You can use lifecycle rules to define when to initiate object deletion based on age or date\. You can create, enable, disable, or delete a lifecycle rule\.

For more information about S3 Lifecycle, see [Managing your storage lifecycle](object-lifecycle-mgmt.md)\.

**Note**  
The AWS account that creates the bucket owns it and is the only one that can create, enable, disable, or delete a lifecycle rule\.

To create and manage a lifecycle rule for an S3 on Outposts by using the AWS Management Console, see the following topics\.

**Topics**
+ [Creating a lifecycle rule](#s3-outposts-bucket-create-lifecycle)
+ [Enabling a lifecycle rule](#s3-outposts-bucket-enable-lifecycle)
+ [Editing a lifecycle rule](#s3-outposts-bucket-edit-lifecycle)
+ [Deleting a lifecycle rule](#s3-outposts-bucket-delete-lifecycle)

## Creating a lifecycle rule<a name="s3-outposts-bucket-create-lifecycle"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Outposts buckets**\.

1. Choose the Outposts bucket that you want to create a lifecycle rule for\.

1. Choose the **Management** tab, and then choose **Create Lifecycle rule**\.

1. Enter a value for **Lifecycle rule name**\.

1. Under **Rule scope**, choose one of the following options:
   + To limit the scope to specific prefixes or tags, choose **Limit the scope to specific prefixes or tags**\. Then, add a prefix filter or tags\.
   + To apply the rule to all objects in the bucket, choose **Apply to all objects in the bucket**\.

1. Under **Lifecycle rule actions**, choose one of the following options:
   + **Expire current versions of objects** – For versioning\-enabled buckets, S3 on Outposts adds a delete marker and retains the objects as noncurrent versions\. For buckets that don't use S3 Versioning, S3 on Outposts permanently deletes the objects\.
   + **Permanently delete noncurrent versions of objects** – S3 on Outposts permanently deletes noncurrent versions of objects\. 
   + **Delete expired object delete markers** – S3 on Outposts permanently deletes expired object delete markers\.

     If you limit the scope of your Lifecycle rule by using object tags, you can't choose **Delete expired object delete markers**\. You also can't choose **Delete expired object delete markers** if you choose **Expire current object versions**\.

1. If you chose **Expire current versions of objects** or **Permanently delete noncurrent versions of objects**, configure the rule trigger based on a specific date or the object's age\. 

1. If you chose **Delete expired object delete markers**, to confirm that you want to delete expired object delete markers, select **Delete expired object delete markers**\.

1. Under **Timeline Summary**, review your Lifecycle rule, and choose **Create rule**\.

## Enabling a lifecycle rule<a name="s3-outposts-bucket-enable-lifecycle"></a>

**To enable or disable a bucket lifecycle rule**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Outposts buckets**\.

1. Choose the Outposts bucket that you want to enable or disable a lifecycle rule for\.

1. Choose the **Management** tab, and then under **Lifecycle rule**, choose the rule that you want to enable or disable\.

1. For **Action**, choose **Enable or disable rule**\.

## Editing a lifecycle rule<a name="s3-outposts-bucket-edit-lifecycle"></a>

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Outposts buckets**\.

1. Choose the Outposts bucket that you want to edit a lifecycle rule for\.

1. Choose the **Management** tab, and then choose the **Lifecycle rule** that you want to edit\.

1. \(Optional\) Update the value for **Lifecycle rule name**\.

1. Under **Rule scope**, edit the scope as needed:
   + To limit the scope to specific prefixes or tags, choose **Limit the scope to specific prefixes or tags**\. Then, add a prefix filter or tags\.
   + To apply the rule to all objects in the bucket, choose **Apply to all objects in the bucket**\.

1. Under **Lifecycle rule actions**, choose one of the following options:
   + **Expire current versions of objects** – For versioning\-enabled buckets, S3 on Outposts adds a delete marker and retains the objects as noncurrent versions\. For buckets that don't use S3 Versioning, S3 on Outposts permanently deletes the objects\.
   + **Permanently delete noncurrent versions of objects** – S3 on Outposts permanently deletes noncurrent versions of objects\. 
   + **Delete expired object delete markers** – S3 on Outposts permanently deletes expired object delete markers\.

     If you limit the scope of your Lifecycle rule by using object tags, you can't choose **Delete expired object delete markers**\. You also can't choose **Delete expired object delete markers** if you choose **Expire current object versions**\.

1. If you chose **Expire current versions of objects** or **Permanently delete noncurrent versions of objects**, configure the rule trigger based on a specific date or the object age\. 

1. If you chose **Delete expired object delete markers**, to confirm that you want to delete expired object delete markers, select **Delete expired object delete markers**\.

1. Choose **Save**\.

## Deleting a lifecycle rule<a name="s3-outposts-bucket-delete-lifecycle"></a>

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Outposts buckets**\.

1. Choose the Outposts bucket that you want to delete a lifecycle rule for\.

1. Choose the **Management** tab, and then under **Lifecycle rule**, choose the rule that you want to delete\.

1. Choose **Delete**\.