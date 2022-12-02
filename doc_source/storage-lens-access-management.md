# Using S3 Storage Lens to audit Object Ownership settings<a name="storage-lens-access-management"></a>

Amazon S3 Object Ownership is an S3 bucket\-level setting that you can use to disable access control lists \(ACLs\) and control ownership of the objects in your bucket\. If you set Object Ownership to bucket owner enforced, you can disable [access control lists \(ACLs\)](acl-overview.md) and take ownership of every object in your bucket\. This approach simplifies access management for data stored in Amazon S3\. 

By default, when another AWS account uploads an object to your S3 bucket, that account \(the object writer\) owns the object, has access to it, and can grant other users access to it through ACLs\. You can use Object Ownership to change this default behavior\. 

A majority of modern use cases in Amazon S3 no longer require the use of ACLs\. Therefore, we recommend that you disable ACLs, except in unusual circumstances where you must control access for each object individually\. By setting Object Ownership to bucket owner enforced, you can disable ACLs and rely on policies for access control\. For more information, see [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\.

With S3 Storage Lens access\-management metrics, you can identify buckets that don't have disabled ACLs\. After identifying these buckets, you can migrate ACL permissions to policies and disable ACLs for these buckets\.

**Topics**
+ [Step 1: Identify general trends for Object Ownership settings](#storage-lens-access-management-step1)
+ [Step 2: Identify bucket\-level trends for Object Ownership settings](#storage-lens-access-management-step2)
+ [Step 3: Update your Object Ownership setting to bucket owner enforced to disable ACLs](#storage-lens-access-management-step3)

## Step 1: Identify general trends for Object Ownership settings<a name="storage-lens-access-management-step1"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the name of the dashboard that you want to view\.

1. In the **Snapshot for *date*** section, under **Metrics categories**, choose **Access management**\.

   The **Snapshot for *date*** section updates to display the **% Object Ownership bucket owner enforced** metric\. You can see the overall percentage of buckets in your account or organization that use the bucket owner enforced setting for Object Ownership to disable ACLs\.

## Step 2: Identify bucket\-level trends for Object Ownership settings<a name="storage-lens-access-management-step2"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the name of the dashboard that you want to view\.

1. To view more detailed bucket\-level metrics, choose the **Bucket** tab\.

1. In the **Distribution by buckets for *date*** section, choose the **% Object Ownership bucket owner enforced** metric\.

   The chart updates to show a per\-bucket breakdown for **% Object Ownership bucket owner enforced**\. You can see which buckets use the bucket owner enforced setting for Object Ownership to disable ACLs\.

1. To view the bucket owner enforced settings in context, scroll down to the **Buckets** section\. For **Metrics categories**, select **Access management**\. Then clear **Summary**\.

   The **Buckets** list displays data for all three Object Ownership settings: bucket owner enforced, bucket owner preferred, and object writer\.

1. To filter the **Buckets** list to display metrics only for a specific Object Ownership setting, choose the preferences icon \(![\[A screenshot that shows the preferences icon in the S3 Storage Lens dashboard.\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/preferences.png)\)\.

1. Clear the metrics that you don't want to see\.

1. \(Optional\) Under **Page size**, choose the number of buckets to display in the list\.

1. Choose **Confirm**\.

## Step 3: Update your Object Ownership setting to bucket owner enforced to disable ACLs<a name="storage-lens-access-management-step3"></a>

After you've identified buckets that use the object writer and bucket owner preferred setting for Object Ownership, you can migrate your ACL permissions to bucket policies\. When you've finished migrating your ACL permissions, you can then update your Object Ownership settings to bucket owner enforced in order to disable ACLs\. For more information, see [Prerequisites for disabling ACLs](object-ownership-migrating-acls-prerequisites.md)\.