# S3 Storage Lens data protection<a name="storage-lens-data-protection"></a>

You can use Amazon S3 Storage Lens data\-protection metrics to identify buckets where data\-protection best practices haven't been applied\. You can use these metrics to take action and apply standard settings that align with best practices for protecting your data across the buckets in your account or organization\. For example, you can use data\-protection metrics to identify buckets that don't use AWS Key Management Service \(AWS KMS\) keys \(SSE\-KMS\) for default encryption or requests that use AWS Signature Version 2 \(SigV2\)\. 

The following use cases provide strategies for using your S3 Storage Lens dashboard to identify outliers and apply data\-protection best practices across your S3 buckets\.

**Topics**
+ [Identify buckets that don't use server\-side encryption with AWS KMS for default encryption \(SSE\-KMS\)](#storage-lens-sse-kms)
+ [Identify buckets that have S3 Versioning enabled](#storage-lens-data-protection-versioning)
+ [Identify requests that use AWS Signature Version 2 \(SigV2\)](#storage-lens-data-protection-sigv)
+ [Count the total number of replication rules for each bucket](#storage-lens-data-protection-replication-rule)
+ [Identify percentage of Object Lock bytes](#storage-lens-data-protection-object-lock)

## Identify buckets that don't use server\-side encryption with AWS KMS for default encryption \(SSE\-KMS\)<a name="storage-lens-sse-kms"></a>

With Amazon S3 default encryption, you can set the default encryption behavior for an S3 bucket\. For more information, see [Setting default server\-side encryption behavior for Amazon S3 buckets](bucket-encryption.md)\.

You can use the **SSE\-KMS enabled bucket count** and **% SSE\-KMS enabled buckets** metrics to identify buckets that use server\-side encryption with AWS KMS keys \(SSE\-KMS\) for default encryption\. S3 Storage Lens also provides metrics for unencrypted bytes, unencrypted objects, encrypted bytes, and encrypted objects\. For a complete list of metrics, see [Amazon S3 Storage Lens metrics glossary](storage_lens_metrics_glossary.md)\. 

You can analyze SSE\-KMS encryption metrics in the context of general encryption metrics to identify buckets that don't use SSE\-KMS\. If you want to use SSE\-KMS for all the buckets in your account or organization, you can then update the default encryption settings for these buckets to use SSE\-KMS\. In addition to SSE\-KMS, you can use server\-side encryption with Amazon S3 managed keys \(SSE\-S3\) or customer\-provided keys \(SSE\-C\)\. For more information, see [Protecting data using encryption](UsingEncryption.md)\. 

### Step 1: Identify which buckets are using SSE\-KMS for default encryption<a name="storage-lens-sse-kms-step1"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the name of the dashboard that you want to view\.

1. In the **Trends and distributions** section, choose **% SSE\-KMS enabled bucket count** for the primary metric and **% encrypted bytes** for the secondary metric\.

   The **Trend for *date*** chart updates to display trends for SSE\-KMS and encrypted bytes\. 

1. To view more granular, bucket\-level insights for SSE\-KMS:

   1. Choose a point on the chart\. A box will appear with choices for more granular insights\.

   1. Choose the **Buckets** dimension\. Then choose **Apply**\.

1. In the **Distribution by buckets for *date*** chart, choose the **SSE\-KMS enabled bucket count** metric\. 

1. You can now see which buckets have SSE\-KMS enabled and which do not\.

### Step 2: Update bucket default encryption settings<a name="storage-lens-sse-kms-step2"></a>

Now that you've determined which buckets use SSE\-KMS in the context of your **% encrypted bytes**, you can identify buckets that don't use SSE\-KMS\. You can then optionally navigate to these buckets within the S3 console and update their default encryption settings to use SSE\-KMS or SSE\-S3\. For more information, see [Configuring default encryption](default-bucket-encryption.md)\.

## Identify buckets that have S3 Versioning enabled<a name="storage-lens-data-protection-versioning"></a>

When enabled, the S3 Versioning feature retains multiple versions of the same object that can be used to quickly recover data if an object is accidentally deleted or overwritten\. You can use the **Versioning\-enabled bucket count** metric to see which buckets use S3 Versioning\. Then, you can take action in the S3 console to enable S3 Versioning for other buckets\.

### Step 1: Identify buckets that have S3 Versioning enabled<a name="storage-lens-data-protection-versioning-step1"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the name of the dashboard that you want to view\.

1. In the **Trends and distributions** section, choose **Versioning\-enabled bucket count** for the primary metric and **Buckets** for the secondary metric\.

   The **Trend for *date*** chart updates to display trends for S3 Versioning enabled buckets\. Right below the trends line, you can see the **Storage class distribution** and **Region distribution** subsections\.

1. To view more granular insights for any of the buckets that you see in the **Trend for *date*** chart so that you can perform a deeper analysis, do the following:

   1. Choose a point on the chart\. A box will appear with choices for more granular insights\.

   1. Choose a dimension to apply to your data for deeper analysis: **Account**, **AWS Region**, **Storage class**, or **Bucket**\. Then choose **Apply**\.

1. In the **Bubble analysis by buckets for *date*** section, choose the **Versioning\-enabled bucket count**, **Buckets**, and **Active buckets** metrics\.

   The **Bubble analysis by buckets for *date*** section updates to display data for the metrics that you selected\. You can use this data to see which buckets have S3 Versioning enabled in the context of your total bucket count\. In the **Bubble analysis by buckets for *date*** section, you can plot your buckets on multiple dimensions by using any three metrics to represent the **X\-axis**, **Y\-axis**, and **Size** of the bubble\. 

### Step 2: Enable S3 Versioning<a name="storage-lens-data-protection-versioning-step2"></a>

After you've identified buckets that have S3 Versioning enabled, you can identify buckets that have never had S3 Versioning enabled or are versioning suspended\. Then, you can optionally enable versioning for these buckets in the S3 console\. For more information, see [Enabling versioning on buckets](manage-versioning-examples.md)\.

## Identify requests that use AWS Signature Version 2 \(SigV2\)<a name="storage-lens-data-protection-sigv"></a>

You can use the **All unsupported signature requests** metric to identify requests that use AWS Signature Version 2 \(SigV2\)\. This data can help you identify specific applications that are using SigV2\. You can then migrate these applications to AWS Signature Version 4 \(SigV4\)\. 

SigV4 is the recommended signing method for all new S3 applications\. SigV4 provides improved security and is supported in all AWS Regions\. For more information, see [Amazon S3 update \- SigV2 deprecation period extended & modified](http://aws.amazon.com/blogs/aws/amazon-s3-update-sigv2-deprecation-period-extended-modified/)\.

**Prerequisite**  
To see **All unsupported signature requests** in your S3 Storage Lens dashboard, you must enable S3 Storage Lens **Advanced metrics and recommendations** and then select **Advanced data protection metrics**\. For more information, see [Creating and updating Amazon S3 Storage Lens dashboards](storage_lens_console_creating_editing.md)\.

### Step 1: Examine SigV2 signing trends by AWS account, Region, and bucket<a name="storage-lens-data-protection-sigv-step1"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the name of the dashboard that you want to view\.

1. To identify specific buckets, accounts, and Regions with requests that use SigV2:

   1. Under **Top N overview for *date***, in **Top N**, enter the number of buckets that you would like to see data for\. 

   1. For **Metric**, choose **All unsupported signature requests** from the **Data protection** category\.

      The **Top N overview for *date*** updates to display data for SigV2 requests by account, AWS Region, and bucket\. The **Top N overview for *date*** section also shows the percentage change from the prior day or week and a spark\-line to visualize the trend\. This trend is a 14\-day trend for free metrics and a 30\-day trend for advanced metrics and recommendations\. 
**Note**  
With S3 Storage Lens advanced metrics and recommendations, metrics are available for queries for 15 months\. For more information, see [Metrics selection](storage_lens_basics_metrics_recommendations.md#storage_lens_basics_metrics_selection)\.

### Step 2: Identify buckets that are accessed by applications through SigV2 requests<a name="storage-lens-data-protection-sigv-step2"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the name of the dashboard that you want to view\.

1. In your Storage Lens dashboard, choose the **Bucket** tab\.

1. Scroll down to the **Buckets** section\. Under **Metrics categories**, choose **Data protection**\. Then clear **Summary**\.

   The **Buckets** list updates to display all the available **Data protection** metrics for the buckets shown\. 

1. To filter the **Buckets** list to display only specific data\-protection metrics, choose the preferences icon \(![\[A screenshot that shows the preferences icon in the S3 Storage Lens dashboard.\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/preferences.png)\)\.

1. Clear the toggles for all data\-protection metrics until only the following metrics remain selected:
   + **All unsupported signature requests**
   + **% all unsupported signature requests**

1. \(Optional\) Under **Page size**, choose the number of buckets to display in the list\.

1. Choose **Confirm**\.

   The **Buckets** list updates to display bucket\-level metrics for SigV2 requests\. You can use this data to identify specific buckets that have SigV2 requests\. Then, you can use this information to migrate your applications to SigV4\. For more information, see [Authenticating Requests \(AWS Signature Version 4\)](https://docs.aws.amazon.com/AmazonS3/latest/API/sig-v4-authenticating-requests.html) in the *Amazon Simple Storage Service API Reference*\.

## Count the total number of replication rules for each bucket<a name="storage-lens-data-protection-replication-rule"></a>

S3 Replication enables automatic, asynchronous copying of objects across Amazon S3 buckets\. Buckets that are configured for object replication can be owned by the same AWS account or by different accounts\. For more information, see [Replicating objects](replication.md)\. 

You can use S3 Storage Lens replication rule count metrics to get detailed per\-bucket information about your buckets that are configured for replication\. This information includes replication rules within and across buckets and Regions\.

**Prerequisite**  
To see replication rule count metrics in your S3 Storage Lens dashboard, you must enable S3 Storage Lens **Advanced metrics and recommendations** and then select **Advanced data protection metrics**\. For more information, see [Creating and updating Amazon S3 Storage Lens dashboards](storage_lens_console_creating_editing.md)\.

### Step 1: Count the total number of replication rules for each bucket<a name="storage-lens-data-protection-replication-rule-step1"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the name of the dashboard that you want to view\.

1. In your Storage Lens dashboard, choose the **Bucket** tab\.

1. Scroll down to the **Buckets** section\. Under **Metrics categories**, choose **Data protection**\. Then clear **Summary**\.

1. To filter the **Buckets** list to display only replication rule count metrics, choose the preferences icon \(![\[A screenshot that shows the preferences icon in the S3 Storage Lens dashboard.\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/preferences.png)\)\.

1. Clear the toggles for all data\-protection metrics until only the replication rule count metrics remain selected:
   + **Same\-Region Replication rule count**
   + **Cross\-Region Replication rule count**
   + **Same\-account replication rule count**
   + **Cross\-account replication rule count**
   + **Total replication rule count**

1. \(Optional\) Under **Page size**, choose the number of buckets to display in the list\.

1. Choose **Confirm**\.

### Step 2: Add replication rules<a name="storage-lens-data-protection-replication-rule-step2"></a>

After you have a per\-bucket replication rule count, you can optionally create additional replication rules\. For more information, see [Walkthroughs: Examples for configuring replication](replication-example-walkthroughs.md)\.

## Identify percentage of Object Lock bytes<a name="storage-lens-data-protection-object-lock"></a>

With S3 Object Lock, you can store objects by using a *write\-once\-read\-many \(WORM\)* model\. You can use Object Lock to help prevent objects from being deleted or overwritten for a fixed amount of time or indefinitely\. You can enable Object Lock only when you create a bucket and also enable S3 Versioning\. However, you can edit the retention period for individual object versions or apply legal holds for buckets that have Object Lock enabled\. For more information, see [Using S3 Object Lock](object-lock.md)\.

You can use Object Lock metrics in S3 Storage Lens to see the **% Object Lock bytes** metric for your account or organization\. You can use this information to identify buckets in your account or organization that aren't following your data\-protection best practices\. 

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the name of the dashboard that you want to view\.

1. In the **Snapshot** section, under **Metrics categories**, choose **Data protection**\.

   The **Snapshot** section updates to display data\-protection metrics, including the **% Object Lock bytes** metric\. You can see the overall percentage of Object Lock bytes for your account or organization\. 

1. To see the **% Object Lock bytes** per bucket, scroll down to the **Top N overview** section\.

   To get object\-level data for Object Lock, you can also use the **Object Lock object count** and **% Object Lock objects** metrics\. 

1. For **Metric**, choose **% Object Lock bytes** from the **Data protection** category\.

   By default, the **Top N overview for *date*** section displays metrics for the top 3 buckets\. In the **Top N** field, you can increase the number of buckets\. The **Top N overview for *date*** section also shows the percentage change from the prior day or week and a spark\-line to visualize the trend\. This trend is a 14\-day trend for free metrics and a 30\-day trend for advanced metrics and recommendations\. 
**Note**  
With S3 Storage Lens advanced metrics and recommendations, metrics are available for queries for 15 months\. For more information, see [Metrics selection](storage_lens_basics_metrics_recommendations.md#storage_lens_basics_metrics_selection)\.

1. Review the following data for **% Object Lock bytes**:
   + **Top *number* accounts** ‐ See which accounts have the highest and lowest **% Object Lock bytes**\.
   + **Top *number* Regions** ‐ View a breakdown of **% Object Lock bytes** by Region\.
   + **Top *number* buckets** ‐ See which buckets have the highest and lowest **% Object Lock bytes**\.