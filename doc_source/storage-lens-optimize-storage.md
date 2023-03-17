# Using Amazon S3 Storage Lens to optimize your storage costs<a name="storage-lens-optimize-storage"></a>

You can use S3 Storage Lens cost\-optimization metrics to reduce the overall cost of your S3 storage\. Cost\-optimization metrics can help you confirm that you've configured Amazon S3 cost effectively and according to best practices\. For example, you can identify the following cost\-optimization opportunities: 
+ Buckets with incomplete multipart uploads older than 7 days
+ Buckets that are accumulating numerous noncurrent versions
+ Buckets that don't have lifecycle rules to abort incomplete multipart uploads
+ Buckets that don't have lifecycle rules to expire noncurrent versions objects
+ Buckets that don't have lifecycle rules to transition objects to a different storage class

You can then use this data to add additional lifecycle rules to your buckets\. 

The following examples show how you can use cost\- optimization metrics in your S3 Storage Lens dashboard to optimize your storage costs\.

**Topics**
+ [Identify your largest S3 buckets](#identify-largest-s3-buckets)
+ [Uncover cold Amazon S3 buckets](#uncover-cold-buckets)
+ [Locate incomplete multipart uploads](#locate-incomplete-mpu)
+ [Reduce the number of noncurrent versions retained](#reduce-noncurrent-versions-retained)
+ [Identify buckets that don't have lifecycle rules and review lifecycle rule counts](#identify-missing-lifecycle-rules)

## Identify your largest S3 buckets<a name="identify-largest-s3-buckets"></a>

You pay for storing objects in S3 buckets\. The rate that you're charged depends on your objects' sizes, how long you store the objects, and their storage classes\. With S3 Storage Lens, you get a centralized view of all the buckets in your account\. To see all the buckets in all of your organization's accounts, you can configure an AWS Organizations\-level S3 Storage Lens dashboard\. From this dashboard view, you can identify your largest buckets\.

### Step 1: Identify your largest buckets<a name="optimize-storage-identify-largest-buckets"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the dashboard that you want to view\.

   When the dashboard opens, you can see the latest date that S3 Storage Lens has collected metrics for\. Your dashboard always loads to the latest date that has metrics available\.

1. To see a ranking of your largest buckets by the **Total storage** metric for a selected date range, scroll down to the **Top N overview for *date*** section\.

   You can toggle the sort order to show the smallest buckets\. You can also adjust the **Metric** selection to rank your buckets by any of the available metrics\. The **Top N overview for *date*** section also shows the percentage change from the prior day or week and a spark\-line to visualize the trend\. This trend is a 14\-day trend for free metrics and a 30\-day trend for advanced metrics and recommendations\.
**Note**  
With S3 Storage Lens advanced metrics and recommendations, metrics are available for queries for 15 months\. For more information, see [Metrics selection](storage_lens_basics_metrics_recommendations.md#storage_lens_basics_metrics_selection)\.

1. For more detailed insights about your buckets, scroll up to the top of the page, and then choose the **Bucket** tab\. 

   On the **Bucket** tab, you can see details such as the recent growth rate, the average object size, the largest prefixes, and the number of objects\.

### Step 2: Navigate to your buckets and investigate<a name="optimize-storage-investigate"></a>

After you've identified your largest S3 buckets, you can navigate to each bucket within the S3 console to view the objects in the bucket, understand its associated workload, and identify its internal owners\. You can contact the bucket owners to find out whether the growth is expected or whether the growth needs further monitoring and control\.

## Uncover cold Amazon S3 buckets<a name="uncover-cold-buckets"></a>

If you have [S3 Storage Lens advanced metrics](storage_lens_basics_metrics_recommendations.md#storage_lens_basics_metrics_selection) enabled, you can use [activity metrics](storage_lens_basics_metrics_recommendations.md#storage_lens_basics_metrics_types) to understand how cold your S3 buckets are\. A "cold" bucket is one whose storage is no longer accessed \(or very rarely accessed\)\. This lack of activity typically indicates that the bucket's objects aren't frequently accessed\.

Activity metrics, such as **GET Requests** and **Download Bytes**, indicate how often your buckets are accessed each day\. To understand the consistency of the access pattern and to spot buckets that are no longer being accessed at all, you can trend this data over several months\. The **Retrieval rate** metric, which is computed as **Download bytes / Total storage**, indicates the proportion of storage in a bucket that is accessed daily\.

**Note**  
Download bytes are duplicated in cases where the same object is downloaded multiple times during the day\.

**Prerequisite**  
To see activity metrics in your S3 Storage Lens dashboard, you must enable S3 Storage Lens **Advanced metrics and recommendations** and then select **Activity metrics**\. For more information, see [Creating and updating Amazon S3 Storage Lens dashboards](storage_lens_console_creating_editing.md)\.

### Step 1: Identify active buckets<a name="storage-lens-identify-active-buckets"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the dashboard that you want to view\.

1. Choose the **Bucket** tab, and then scroll down to the **Bubble analysis by buckets for *date*** section\.

   In the **Bubble analysis by buckets for *date*** section, you can plot your buckets on multiple dimensions by using any three metrics to represent the **X\-axis**, **Y\-axis**, and **Size** of the bubble\. 

1. To find buckets that have gone cold, for **X\-axis**, **Y\-axis**, and **Size**, choose the **Total storage**, **% retrieval rate**, and **Average object size** metrics\.

1. In the **Bubble analysis by buckets for *date*** section, locate any buckets with retrieval rates of zero \(or near zero\) and a larger relative storage size, and choose the bubble that represents the bucket\. 

   A box will appear with choices for more granular insights\. Do one of the following:

   1. To update the **Bucket** tab to display metrics only for the selected bucket, choose **Drill down**, and then choose **Apply**\. 

   1. To aggregate your bucket\-level data to by account, AWS Region, storage class, or bucket, choose **Analyze by** and then make a choice for **Dimension**\. For example, to aggregate by storage class, choose **Storage class** for **Dimension**\.

   To find buckets that have gone cold, do a bubble analysis using the **Total storage**, **% retrieval rate**, and **Average object size** metrics\. Look for any buckets with retrieval rates of zero \(or near zero\) and a larger relative storage size\. 

   The **Bucket** tab of your dashboard updates to display data for your selected aggregation or filter\. If you aggregated by storage class or another dimension, that new tab opens in your dashboard \(for example, the **Storage class** tab\)\. 

### Step 2: Investigate cold buckets<a name="storage-lens-investigate-buckets"></a>

From here, you can identify the owners of cold buckets in your account or organization and find out if that storage is still needed\. You can then optimize costs by configuring [lifecycle expiration configurations](object-lifecycle-mgmt.md) for these buckets or archiving the data in one of the [Amazon S3 Glacier storage classes](https://docs.aws.amazon.com/amazonglacier/latest/dev/introduction.html)\. 

To avoid the problem of cold buckets going forward, you can [automatically transition your data by using S3 Lifecycle configurations](lifecycle-configuration-examples.md) for your buckets, or you can enable [auto\-archiving with S3 Intelligent\-Tiering](archived-objects.md)\.

You can also use step 1 to identify hot buckets\. Then, you can ensure that these buckets use the correct [S3 storage class](storage-class-intro.md) to ensure that they serve their requests most effectively in terms of performance and cost\.

## Locate incomplete multipart uploads<a name="locate-incomplete-mpu"></a>

You can use multipart uploads to upload very large objects \(up to 5 TB\) as a set of parts for improved throughput and quicker recovery from network issues\. In cases where the multipart upload process doesn't finish, the incomplete parts remain in the bucket \(in an unusable state\)\. These incomplete parts incur storage costs until the upload process is finished, or until the incomplete parts are removed\. For more information, see [Uploading and copying objects using multipart upload](mpuoverview.md)\.

With S3 Storage Lens, you can identify the number of incomplete multipart upload bytes in your account or across your entire organization, including incomplete multipart uploads that are more than 7 days old\. For a complete list of incomplete multipart upload metrics, see [Amazon S3 Storage Lens metrics glossary](storage_lens_metrics_glossary.md)\. 

As a best practice, we recommend configuring lifecycle rules to expire incomplete multipart uploads that are older than a specific number of days\. When you create your lifecycle rule to expire incomplete multipart uploads, we recommend 7 days as a good starting point\. 

### Step 1: Review overall trends for incomplete multipart uploads<a name="locate-incomplete-mpu-step1"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the dashboard that you want to view\.

1. In the **Snapshot for *date*** section, under **Metrics categories**, choose **Cost optimization**\.

   The **Snapshot for *date*** section updates to display **Cost optimization** metrics, which include **Incomplete multipart upload bytes greater than 7 days old**\. 

   In any chart in your S3 Storage Lens dashboard, you can see metrics for incomplete multipart uploads\. You can use these metrics to further assess the impact of incomplete multipart upload bytes on your storage, including their contribution to overall growth trends\. You can also drill down to deeper levels of aggregation, using the **Account**, **AWS Region**, **Bucket**, or **Storage class** tabs for a deeper analysis of your data\. For an example, see [Uncover cold Amazon S3 buckets](#uncover-cold-buckets)\.

### Step 2: Identify buckets that have the most incomplete multipart upload bytes but don't have lifecycle rules to abort incomplete multipart uploads<a name="locate-incomplete-mpu-step2"></a>

**Prerequisite**  
To see the **Abort incomplete multipart upload lifecycle rule count** metric in your S3 Storage Lens dashboard, you must enable S3 Storage Lens **Advanced metrics and recommendations**, and then select **Advanced cost optimization metrics**\. For more information, see [Creating and updating Amazon S3 Storage Lens dashboards](storage_lens_console_creating_editing.md)\.

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the dashboard that you want to view\.

1. To identify specific buckets that are accumulating incomplete multipart uploads greater than 7 days old, go to the **Top N overview for *date*** section\. 

   By default, the **Top N overview for *date*** section displays metrics for the top 3 buckets\. You can increase or decrease the number of buckets in the **Top N** field\. The **Top N overview for *date*** section also shows the percentage change from the prior day or week and a spark\-line to visualize the trend\. \(This trend is a 14\-day trend for free metrics and a 30\-day trend for advanced metrics and recommendations\.\) 
**Note**  
With S3 Storage Lens advanced metrics and recommendations, metrics are available for queries for 15 months\. For more information, see [Metrics selection](storage_lens_basics_metrics_recommendations.md#storage_lens_basics_metrics_selection)\.

1. For **Metric**, choose **Incomplete multipart upload bytes greater than 7 days old** in the **Cost optimization** category\.

   Under **Top *number* buckets**, you can see the buckets with the most incomplete multipart upload storage bytes that are greater than 7 days old\.

1. To view more detailed bucket\-level metrics for incomplete multipart uploads, scroll to the top of the page, and then choose the **Bucket** tab\.

1. Scroll down to the **Buckets** section\. For **Metrics categories**, select **Cost optimization**\. Then clear **Summary**\.

   The **Buckets** list updates to display all the available **Cost optimization** metrics for the buckets shown\. 

1. To filter the **Buckets** list to display only specific cost\-optimization metrics, choose the preferences icon \(![\[A screenshot that shows the preferences icon in the S3 Storage Lens dashboard.\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/preferences.png)\)\.

1. Clear the toggles for all cost\-optimization metrics until only **Incomplete multipart upload bytes greater than 7 days old** and **Abort incomplete multipart upload lifecycle rule count** remain selected\. 

1. \(Optional\) Under **Page size**, choose the number of buckets to display in the list\.

1. Choose **Confirm**\.

   The **Buckets** list updates to display bucket\-level metrics for incomplete multipart uploads and lifecycle rule counts\. You can use this data to identify buckets that have the most incomplete multipart upload bytes that are greater than 7 days old and are missing lifecycle rules to abort incomplete multipart uploads\. Then, you can navigate to these buckets in the S3 console and add lifecycle rules to delete abandoned incomplete multipart uploads\.

### Step 3: Add a lifecycle rule to delete incomplete multipart uploads after 7 days<a name="locate-incomplete-mpu-step3"></a>

To automatically manage incomplete multipart uploads, you can use the S3 console to create a lifecycle configuration to expire incomplete multipart upload bytes from a bucket after a specified number of days\. For more information, see [Configuring a bucket lifecycle configuration to delete incomplete multipart uploads](mpu-abort-incomplete-mpu-lifecycle-config.md)\.

## Reduce the number of noncurrent versions retained<a name="reduce-noncurrent-versions-retained"></a>

When enabled, S3 Versioning retains multiple distinct copies of the same object that you can use to quickly recover data if an object is accidentally deleted or overwritten\. If you've enabled S3 Versioning without configuring lifecycle rules to transition or expire noncurrent versions, a large number of previous noncurrent versions can accumulate, which can have storage\-cost implications\. For more information, see [Using versioning in S3 buckets](Versioning.md)\.

### Step 1: Identify buckets with the most noncurrent object versions<a name="reduce-noncurrent-versions-retained-step1"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the dashboard that you want to view\.

1. In the **Snapshot for *date*** section, under **Metric categories**, choose **Cost optimization**\.

   The **Snapshot for *date*** section updates to display **Cost optimization** metrics, which include the metric for **% noncurrent version bytes**\. The **% noncurrent version bytes** metric represents the proportion of your total storage bytes that is attributed to noncurrent versions, within the dashboard scope and for the selected date\.
**Note**  
If your **% noncurrent version bytes** is greater than 10 percent of your storage at the account level, you might be storing too many object versions\.

1. To identify specific buckets that are accumulating a large number of noncurrent versions:

   1. Scroll down to the **Top N overview for *date*** section\. For **Top N**, enter the number of buckets that you would like to see data for\. 

   1. For **Metric**, choose **% noncurrent version bytes**\.

      Under **Top *number* buckets**, you can see the buckets \(for the number that you specified\) with the highest **% noncurrent version bytes**\. The **Top N overview for *date*** section also shows the percentage change from the prior day or week and a spark\-line to visualize the trend\. This trend is a 14\-day trend for free metrics and a 30\-day trend for advanced metrics and recommendations\. 
**Note**  
With S3 Storage Lens advanced metrics and recommendations, metrics are available for queries for 15 months\. For more information, see [Metrics selection](storage_lens_basics_metrics_recommendations.md#storage_lens_basics_metrics_selection)\.

   1. To view more detailed bucket\-level metrics for noncurrent object versions, scroll to the top of the page, and then choose the **Bucket** tab\.

      In any chart or visualization in your S3 Storage Lens dashboard, you can drill down to deeper levels of aggregation, using the **Account**, **AWS Region**, **Storage class**, or **Bucket** tabs\. For an example, see [Uncover cold Amazon S3 buckets](#uncover-cold-buckets)\.

   1. In the **Buckets** section, for **Metric categories**, select **Cost optimization**\. Then, clear **Summary**\. 

      You can now see the **% noncurrent version bytes** metric, along with other metrics related to noncurrent versions\.

### Step 2: Identify buckets that are missing transition and expiration lifecycle rules for managing noncurrent versions<a name="reduce-noncurrent-versions-retained-step2"></a>

**Prerequisite**  
To see the **Noncurrent version transition lifecycle rule count** and **Noncurrent version expiration lifecycle rule count** metrics in your S3 Storage Lens dashboard, you must enable S3 Storage Lens **Advanced metrics and recommendations**, and then select **Advanced cost optimization metrics**\. For more information, see [Creating and updating Amazon S3 Storage Lens dashboards](storage_lens_console_creating_editing.md)\.

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the dashboard that you want to view\.

1. In your Storage Lens dashboard, choose the **Bucket ** tab\.

1. Scroll down to the **Buckets** section\. For **Metrics categories**, select **Cost optimization**\. Then clear **Summary**\.

   The **Buckets** list updates to display all the available **Cost optimization** metrics for the buckets shown\. 

1. To filter the **Buckets** list to display only specific cost\-optimization metrics, choose the preferences icon \(![\[A screenshot that shows the preferences icon in the S3 Storage Lens dashboard.\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/preferences.png)\)\.

1. Clear the toggles for all cost\-optimization metrics until only the following remain selected:
   + **% noncurrent version bytes**
   + **Noncurrent version transition lifecycle rule count**
   + **Noncurrent version expiration lifecycle rule count**

1. \(Optional\) Under **Page size**, choose the number of buckets to display in the list\.

1. Choose **Confirm**\.

   The **Buckets** list updates to display metrics for noncurrent version bytes and noncurrent version lifecycle rule counts\. You can use this data to identify buckets that have a high percentage of noncurrent version bytes but are missing transition and expiration lifecycle rules\. Then, you can navigate to these buckets in the S3 console and add lifecycle rules to these buckets\.

### Step 3: Add lifecycle rules to transition or expire noncurrent object versions<a name="reduce-noncurrent-versions-retained-step3"></a>

After you've determined which buckets require further investigation, you can navigate to the buckets within the S3 console and add a lifecycle rule to expire noncurrent versions after a specified number of days\. Alternatively, to reduce costs while still retaining noncurrent versions, you can configure a lifecycle rule to transition noncurrent versions to one of the Amazon S3 Glacier storage classes\. For more information, see [Example 6: Specifying a lifecycle rule for a versioning\-enabled bucket ](lifecycle-configuration-examples.md#lifecycle-config-conceptual-ex6)\. 

## Identify buckets that don't have lifecycle rules and review lifecycle rule counts<a name="identify-missing-lifecycle-rules"></a>

S3 Storage Lens provides S3 Lifecycle rule count metrics that you can use to identify buckets that are missing lifecycle rules\. To find buckets that don't have lifecycle rules, you can use the **Total buckets without lifecycle rules** metric\. A bucket with no S3 Lifecycle configuration might have storage that you no longer need or can migrate to a lower\-cost storage class\. You can also use lifecycle rule count metrics to identify buckets that are missing specific types of lifecycle rules, such as expiration or transition rules\.

**Prerequisite**  
To see lifecycle rule count metrics and the **Total buckets without lifecycle rules** metric in your S3 Storage Lens dashboard, you must enable S3 Storage Lens **Advanced metrics and recommendations**, and then select **Advanced cost optimization metrics**\. For more information, see [Creating and updating Amazon S3 Storage Lens dashboards](storage_lens_console_creating_editing.md)\.

### Step 1: Identify buckets without lifecycle rules<a name="identify-missing-lifecycle-rules-step1"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the dashboard that you want to view\.

1. To identify specific buckets without lifecycle rules, scroll down to the **Top N overview for *date*** section\.

   By default, the **Top N overview for *date*** section displays metrics for the top 3 buckets\. In the **Top N** field, you can increase the number of buckets\. The **Top N overview for *date*** section also shows the percentage change from the prior day or week and a spark\-line to visualize the trend\. This trend is a 14\-day trend for free metrics and a 30\-day trend for advanced metrics and recommendations\. 
**Note**  
With S3 Storage Lens advanced metrics and recommendations, metrics are available for queries for 15 months\. For more information, see [Metrics selection](storage_lens_basics_metrics_recommendations.md#storage_lens_basics_metrics_selection)\.

1. For **Metric**, choose **Total buckets without lifecycle rules** from the **Cost optimization** category\.

1. Review the following data for **Total buckets without lifecycle rules**:
   + **Top *number* accounts** ‐ See which accounts that have the most buckets without lifecycle rules\.
   + **Top *number* Regions** ‐ View a breakdown of buckets without lifecycle rules by Region\.
   + **Top *number* buckets** ‐ See which buckets don't have lifecycle rules\. 

   In any chart or visualization in your S3 Storage Lens dashboard, you can drill down to deeper levels of aggregation, using the **Account**, **AWS Region**, **Storage class**, or **Bucket** tabs\. For an example, see [Uncover cold Amazon S3 buckets](#uncover-cold-buckets)\.

   After you identify which buckets don't have lifecycle rules, you can also review specific lifecycle rule counts for your buckets\. 

### Step 2: Review lifecycle rule counts for your buckets<a name="identify-missing-lifecycle-rules-step2"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the dashboard that you want to view\.

1. In your S3 Storage Lens dashboard, choose the **Bucket** tab\.

1. Scroll down to the **Buckets** section\. Under **Metrics categories**, select **Cost optimization**\. Then clear **Summary**\.

   The **Buckets** list updates to display all the available **Cost optimization** metrics for the buckets shown\. 

1. To filter the **Buckets** list to display only specific cost\-optimization metrics, choose the preferences icon \(![\[A screenshot that shows the preferences icon in the S3 Storage Lens dashboard.\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/preferences.png)\)\.

1. Clear the toggles for all cost\-optimization metrics until only the following remain selected:
   + **Transition lifecycle rule count**
   + **Expiration lifecycle rule count**
   + **Noncurrent version transition lifecycle rule count**
   + **Noncurrent version expiration lifecycle rule count**
   + **Abort incomplete multipart upload lifecycle rule count**
   + **Total lifecycle rule count**

1. \(Optional\) Under **Page size**, choose the number of buckets to display in the list\.

1. Choose **Confirm**\.

   The **Buckets** list updates to display lifecycle rule count metrics for your buckets\. You can use this data to identify buckets without lifecycle rules or buckets that are missing specific kinds of lifecycle rules, for example, expiration or transition rules\. Then, you can navigate to these buckets in the S3 console and add lifecycle rules to these buckets\.

### Step 3: Add lifecycle rules<a name="identify-missing-lifecycle-rules-step3"></a>

After you've identified buckets with no lifecycle rules, you can add lifecycle rules\. For more information, see [Setting lifecycle configuration on a bucket](how-to-set-lifecycle-configuration-intro.md) and [Examples of S3 Lifecycle configuration](lifecycle-configuration-examples.md)\.