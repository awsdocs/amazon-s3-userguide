# Using Amazon S3 Storage Lens to optimize your storage costs<a name="storage-lens-optimize-storage"></a>

Amazon S3 Storage Lens aggregates your usage and activity metrics and displays that information in the account snapshot on the Amazon S3 console **Buckets** \(home\) page\. You can use the S3 Storage Lens dashboard to visualize insights and trends, flag outliers, and receive recommendations to optimize storage costs and apply data protection best practices\. 

The following use cases provide strategies for using the S3 Storage Lens dashboard to optimize your storage more effectively\.

**Topics**
+ [Identify your largest S3 buckets](#identify-largest-s3-buckets)
+ [Locate incomplete multipart uploads](#locate-incomplete-mpu)
+ [Reduce the number of noncurrent versions retained](#reduce-noncurrent-versions-retained)
+ [Uncover cold Amazon S3 buckets](#uncover-cold-buckets)

## Identify your largest S3 buckets<a name="identify-largest-s3-buckets"></a>

You pay for storing objects in S3 buckets\. The rate you’re charged depends on your objects' sizes, how long you store the objects, and their storage classes\. With Amazon S3 Storage Lens, you get a centralized view of all the buckets in your account\. To see all the buckets in all of your organization's accounts, you can configure an AWS Organizations\-level S3 Storage Lens dashboard\. From this dashboard view, you can identify your largest buckets\.

**To identify your largest buckets**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the dashboard that you want to view\.

1. In the upper\-right corner, you see the latest date that S3 Storage Lens has collected storage metrics for\. Your dashboard always loads for the latest date for which metrics are available\.
   + To adjust the scope of the dashboard data that you are viewing, choose **Filters** to apply temporary filters\.
   + To remove all filters, choose **Reset**, and then choose **Apply**\.

1. On the **Overview** tab of your dashboard, scroll down to the **Top N overview for *date*** section to see a ranking of your largest buckets by the **Total storage** metric for a selected date range\. 

   You can toggle the sort order to show the smallest buckets and adjust the metric to rank your buckets by any of the more than 30 metrics available\. This view also shows the percentage change from the prior day or week, as well as a spark\-line to visualize your 14\-day trend \(or 30\-day trend if you’ve upgraded to **advanced metrics and recommendations**\)\. 

1. For more detailed insights about your buckets, choose the **Buckets** tab of this dashboard\. On the **Buckets** tab, you can see details such as the recent growth rate, the average object size, the largest prefixes, and the number of objects\.

1. For your largest buckets, you can then navigate to each bucket within the S3 console to understand its objects and associated workload or to identify internal owners of the bucket\. From the bucket owners, you can find out whether this growth is expected, or if this growth needs further monitoring and control\.

## Locate incomplete multipart uploads<a name="locate-incomplete-mpu"></a>

You can use the multipart upload feature to upload very large objects \(up to 5 TB\) as a set of parts for improved throughput and quicker recovery from network issues\. In cases where the multipart upload process doesn’t finish, the incomplete parts remain in the bucket \(in an unusable state\) and incur storage costs until the upload process is finished, or until the incomplete parts are removed\. For more information, see [Uploading and copying objects using multipart upload](mpuoverview.md)\.

With S3 Storage Lens, you can identify the number of incomplete multipart upload bytes in your account or across your entire organization\.

**To identify incomplete multipart upload bytes**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the dashboard that you want to view\.

1. At the top of the **Overview** tab in the **Snapshot** section, choose **Cost efficiency** to see the **% incomplete MPU bytes** metric\.

You can also select **Incomplete multipart upload bytes** as a metric in any chart in the S3 Storage Lens dashboard\. You can then further assess the impact of incomplete multipart upload bytes on your storage, including their contribution to overall growth trends, or you can identify specific buckets that are accumulating incomplete multipart uploads\.

To automatically manage incomplete multipart uploads, [create a lifecycle policy to expire incomplete multipart upload bytes](https://docs.aws.amazon.com/AmazonS3/latest/userguide/mpu-abort-incomplete-mpu-lifecycle-config.html) from the bucket after a specified number of days\.

## Reduce the number of noncurrent versions retained<a name="reduce-noncurrent-versions-retained"></a>

When enabled, the S3 Versioning feature retains multiple versions of the same object that can be used to quickly recover data if an object is accidentally deleted or overwritten\. S3 Versioning can have storage cost implications if a large number of previous noncurrent versions have accumulated\. For more information, see [Using versioning in S3 buckets](Versioning.md)\.



**To identify the accumulation of your noncurrent versioned objects**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the dashboard that you want to view\.

1. At the top of the **Overview** tab in the **Snapshot** section, choose **Cost efficiency**\. The metric for **% noncurrent version bytes** represents the proportion of your total storage bytes \(within the scope of the dashboard\) that is attributed to noncurrent versions, for the selected date\.
**Note**  
If your **% noncurrent version bytes** is greater than 10 percent of your storage at the account level, it could be an indicator that you’re storing too many versions\.

1. To identify specific buckets that are accumulating a large number of noncurrent versions, scroll down to the **Top N overview for *date*** section, and select the **% noncurrent version bytes** metric\.

After you’ve determined which buckets require further investigation, you can navigate to the buckets within the S3 console and enable a lifecycle policy to expire noncurrent versions after a specified number of days\. Alternatively, to reduce costs while still retaining noncurrent versions, you can configure a lifecycle policy to transition noncurrent versions to Amazon S3 Glacier\. For more information, see [Example 6: Specifying a lifecycle rule for a versioning\-enabled bucket ](lifecycle-configuration-examples.md#lifecycle-config-conceptual-ex6)\. 

## Uncover cold Amazon S3 buckets<a name="uncover-cold-buckets"></a>

If you have [S3 Storage Lens advanced metrics](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage_lens_basics_metrics_recommendations.html) enabled, you can use [activity metrics](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage_lens_basics_metrics_recommendations.html) to understand how cold your S3 buckets are\. A “cold” bucket is one whose storage is no longer accessed \(or very rarely accessed\)\. This lack of activity typically indicates that the bucket's objects aren't frequently accessed\.

Activity metrics, such as **GET Requests** and **Download Bytes**, indicate how often your buckets are accessed each day\. To understand the consistency of the access pattern and to spot buckets that are no longer being accessed at all, you can trend this data over several months\. The **Retrieval rate** metric, which is computed as **Download bytes / Total storage**, indicates the proportion of storage in a bucket that is accessed daily\.

**Note**  
Download bytes are duplicated in cases where the same object is downloaded multiple times during the day\.



**To see how active your buckets are**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the dashboard that you want to view\.

1. Choose the **Bucket** tab of the dashboard and scroll down to the **Bubble analysis by buckets for *date*** charts\.

1. In the **Bubble analysis** section, you can plot your buckets on multiple dimensions using any three metrics to represent the **x\-axis**, **y\-axis**, and **size** of the bubble\. Select **% retrieval rate** as one of the metrics\.

1. To find buckets that have gone cold, do a bubble analysis using the **Total storage**, **% retrieval rate**, and **Average object size** metrics\. Look for any buckets with retrieval rates of zero \(or near zero\) and a larger relative storage size\. 

From here, you can identify the bucket owners in your account or organization and find out if that storage is still needed\. You can then optimize costs by configuring [lifecycle expiration policies](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.html) for the buckets or archiving the data in [Amazon S3 Glacier](https://docs.aws.amazon.com/amazonglacier/latest/dev/introduction.html)\. 

To avoid the problem of cold buckets going forward, you can [automatically transition your data using S3 Lifecycle policies](https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-configuration-examples.html) for your buckets, or you can enable [auto\-archiving with S3 Intelligent\-Tiering](https://docs.aws.amazon.com/AmazonS3/latest/userguide/archived-objects.html)\.

Conversely, using the preceding example, you can identify hot buckets and see if they have been optimized to serve their requests most effectively by ensuring that the correct [S3 storage class](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html) is being used for them\.