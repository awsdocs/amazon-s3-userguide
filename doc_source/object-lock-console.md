# Configuring S3 Object Lock using the console<a name="object-lock-console"></a>

With S3 Object Lock, you can store objects in Amazon S3 using a *write\-once\-read\-many* \(WORM\) model\. You can use S3 Object Lock to prevent an object from being deleted or overwritten for a fixed amount of time or indefinitely\. For more information about S3 Object Lock capabilities, see [How S3 Object Lock works](object-lock-overview.md)\.

To use S3 Object Lock, follow these basic steps:

1. Create a new bucket with Object Lock enabled\.

1. \(Optional\) Configure a default retention period for objects placed in the bucket\.

1. Place the objects that you want to lock in the bucket\.

1. Apply a retention period, a legal hold, or both, to the objects that you want to protect\.

Before you lock any objects, you have to enable a bucket to use S3 Object Lock\. You enable Object Lock when you create a bucket\. After you enable Object Lock on a bucket, you can lock objects in that bucket\. When you create a bucket with Object Lock enabled, you can't disable Object Lock or suspend versioning for that bucket\. 

For information about creating a bucket with S3 Object Lock enabled, see [Creating a bucket](create-bucket-overview.md)\.

**To enable Object Lock legal hold**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want\.

1. In the **Objects** list, choose the name of the object that you want to enable or disable legal hold for\.

   The **Object overview** opens, displaying the properties for your object\.

1. Under **Object Lock legal hold**, choose **Edit**\. 

1. Under **Legal hold**, choose **Enable** or **Disable**\.

1. Choose **Save changes**\.

**To edit Object Lock retention settings**

1. In the **Objects** list, choose the name of the object that you want to edit Object Lock retention settings for\.

   The **Object overview** opens, displaying the properties for your object\.

1. Under **Object Lock retention**, choose **Edit**\. 

1. Under **Retention**, choose **Enable** or **Disable**\.

1. Under **Retention mode**, choose **Governance mode** or **Compliance mode**\.

1. In the **Retain until date** box, enter the date when the object is no longer protected by the chosen retention mode\.

1. Choose **Save changes**\.

For more information about legal hold and retention settings, see [How S3 Object Lock works](object-lock-overview.md)\.

For information about managing Object Lock using the AWS CLI, AWS SDKs, and the Amazon S3 REST APIs, see [[Managing Object Lock ](object-lock-managing.md)Managing Object Lock ](object-lock-managing.md)\.