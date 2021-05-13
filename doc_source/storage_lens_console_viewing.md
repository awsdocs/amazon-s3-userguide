# Viewing an Amazon S3 Storage Lens dashboard<a name="storage_lens_console_viewing"></a>

Amazon S3 Storage Lens aggregates your usage and activity metrics and displays the information in the account snapshot on the Amazon S3 console home \(**Buckets**\) page, interactive dashboards, or through a metrics export that you can download in CSV or Parquet format\. You can use the dashboard to visualize insights and trends, flag outliers, and receive recommendations for optimizing storage costs and applying data protection best practices\. You can use S3 Storage Lens through the AWS Management Console, AWS CLI, AWS SDKs, or REST API\.

 The Amazon S3 Storage Lens default dashboard is **default\-account\-dashboard**\. This dashboard is preconfigured by Amazon S3 to help you visualize summarized insights and trends for your entire account’s aggregated storage usage and activity metrics on the console\. You can't modify its configuration scope, but you can upgrade the metrics selection from the Free Metrics to the paid Advanced Metrics and Recommendations, configure the optional metrics export, or even disable it\. The default dashboard cannot be deleted\. 

 You can also create additional S3 Storage Lens dashboards that are focused on specific AWS Regions, S3 buckets, or other accounts in your organizations\. 

 The Amazon S3 dashboard provides a rich resource of information about its storage scope representing more than 30 metrics\. These metrics represent trends and other information, including storage summary, cost efficiency, data protection, and activity\.

The dashboard always loads for the latest date for which metrics are available\.

**To view an S3 Storage Lens dashboard**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the dashboard that you want to view\.

   In the upper\-right corner, you should see the latest date that S3 Storage Lens has collected storage metrics for\. You also have access to temporary filters to further limit the scope of the dashboard data that you are viewing\. There is also a reset option that you can use to remove all filters\. 

   Your dashboard always loads for the latest date for which metrics are available\.
**Note**  
You can’t use your account's root user credentials to view Amazon S3 Storage Lens dashboards\. To access S3 Storage Lens dashboards, you must grant the requisite IAM permissions to a new or existing IAM user\. Then, sign in with those user credentials to access S3 Storage Lens dashboards\. For more information, see [Setting permissions to use S3 Storage Lens](https://docs.aws.amazon.com/AmazonS3/latest/dev/storage_lens_iam_permissions.html)\. 

## Understanding your S3 Storage Lens dashboard<a name="storage_lens_console_viewing_dashboard"></a>

Your S3 Storage Lens dashboard consists of a primary **Overview** tab, and up to five additional tabs that represent each aggregation level:
+ **Account** \(for organization\-level dashboards only\)
+ **Region**
+ **Storage class**
+ **Bucket**
+ **Prefix** \(only if subscribed to advanced metrics and recommendations\)

Your dashboard data is aggregated into three different sections\.

**Snapshot**  
The first section is the **Snapshot** section, which shows the metrics that S3 Storage Lens has aggregated for the preceding date selected\. It shows aggregated data for the following five metrics from your S3 Storage Lens dashboard’s configuration scope:
+ Total storage bytes
+ Total object count
+ Average object size
+  Accounts – This value is **1** unless you are using AWS Organizations, and your S3 Storage Lens has trusted access with a valid service\-linked role\. For more information, see [Using service\-linked roles for S3 Storage Lens](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-service-linked-roles.html)\. 
+ Buckets
+  Requests – If you chose to use **Advanced metrics and recommendations** for this dashboard\.

The **Metrics** section of the **Snapshot** section shows aggregated data of the storage usage and activity metrics grouped into the following categories:
+ Summary
+ Cost efficiency
+ Data protection
+ Activity

 You can view the relevant properties for these metrics, including **totals**, **% change \(day/day, week/week, and month/month\) trends**, and **recommendations**\.

**Trends and distribution**  
The second section of the **Overview** tab is **Trends and distribution**\.

Trends provide two metrics that you can choose to compare over a date range of your choice aggregated by a period of your choice\. It helps you see the relationship between the two metrics trends over your dashboard storage scope\. You can see the **Storage class** and **Region** distribution between the two trends that you are tracking\.

 With the three different ways of comparing metrics, you can get further insights about your storage that can help you optimize your usage over time\. 

**Top N overview**  
The third section of the S3 Storage Lens dashboard is **Top N overview** \(sorted in ascending or descending order\)\. This lets you see your select metrics across the top *N* accounts \(if you enabled S3 Storage Lens to work with AWS Organizations\)\.

The **Dimension level** tabs provide a detailed view of all values within a particular dimension\. For example, the **Region** tab shows metrics for all AWS Regions, and the **Bucket** tab shows metrics for all buckets\. Each dimension tab contains an identical layout consisting of four sections:
+ A *trend chart* displaying your top *N* items within the dimension over the last 30 days for the selected metric\. By default, this chart displays the top 10 items, but you can increase it to any number that you want\.
+ A *histogram chart* shows a vertical bar chart for the selected date and metric\. You might need to scroll horizontally if you have a very large number of items to display in this chart\.
+ The *bubble analysis chart* plots all items within the dimension by representing the first metric on the x axis, a second metric on the y axis, and a third metric represented by the size of the bubble\. 
+ The *metric grid view* contains each item in the dimension listed in rows\. The columns represent each available metric, arranged in metrics category tabs for easier navigation\. 

**Note**  
To provide a fluid experience in conducting your analysis, the S3 Storage Lens dashboard provides a **drill\-down** action menu, which appears when you choose any chart value\. Choose any chart value to see the associated metrics values, and choose from two options:  
The **drill\-down** action applies the selected value as a filter across all tabs of your dashboard\. You can then drill down into that value for deeper analysis\.
The **analyze\-by** action takes you to the selected dimension tab in your dashboard and applies that value as a filter\. You can then view that value in context of the new dimension for deeper analysis\.
The drill\-down and analyze\-by actions might not appear if the outcome would yield illogical results or would not have any value\. Both the drill\-down and analyze\-by actions result in filters being applied on top of any existing filters across all tabs of the dashboard\. If necessary, you can remove the filters, or use the reset option to remove all filters\.