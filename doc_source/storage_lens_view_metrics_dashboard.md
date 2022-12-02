# Viewing S3 Storage Lens metrics on the dashboards<a name="storage_lens_view_metrics_dashboard"></a>

In the Amazon S3 console, S3 Storage Lens also provides an interactive dashboard that you can use to visualize insights and trends, flag outliers, and receive recommendations for optimizing storage costs and applying data\-protection best practices\. Your dashboard has drill\-down options to generate insights at the organization, account, bucket, object, or prefix level\. The dashboard always loads for the latest date that has metrics available\.

The S3 Storage Lens default dashboard on the console is named **default\-account\-dashboard**\. S3 preconfigures this dashboard to visualize the summarized insights and trends for your entire account and updates them daily in the S3 console\. You can't modify the configuration scope of the default dashboard, but you can upgrade the metrics selection from free metrics to the paid advanced metrics and recommendations\. With advanced metrics and recommendations, you can access additional metrics and features\. These features include advanced metric categories, prefix\-level aggregation, contextual recommendations, and Amazon CloudWatch publishing\.

You can disable the default dashboard, but you can't delete it\. If you disable your default dashboard, it is no longer updated, and you will no longer receive any new daily metrics in S3 Storage Lens or in the **Account snapshot** section on the **Buckets** page\. You can still see historic data in the default dashboard until the 14\-day period for data queries expires\. This period is 15 months if you've enabled advanced metrics and recommendations\. To access this data, you can re\-enable the default dashboard within the expiration period\.

You can create additional S3 Storage Lens dashboards and scope them by AWS Regions, S3 buckets, or accounts \(for AWS Organizations\)\. When you create or edit an S3 Storage Lens dashboard, you define your dashboard scope and metrics selection\. 

 

You can disable or delete any additional dashboards that you create\. 
+ If you disable a dashboard, it is no longer updated, and you will no longer receive any new daily metrics\. You can still see historic data for free metrics until the 14\-day expiration period\. If you enabled advanced metrics and recommendations for that dashboard, this period is 15 months\. To access this data, you can re\-enable the dashboard within the expiration period\. 
+ If you delete your dashboard, you lose all your dashboard configuration settings\. You will no longer receive any new daily metrics, and you also lose access to the historical data associated with that dashboard\. If you want to access the historic data for a deleted dashboard, you must create another dashboard with the same name in the same home Region\.

**Topics**
+ [Viewing an Amazon S3 Storage Lens dashboard](#storage_lens_console_viewing)
+ [Understanding your S3 Storage Lens dashboard](#storage_lens_console_viewing_dashboard)

## Viewing an Amazon S3 Storage Lens dashboard<a name="storage_lens_console_viewing"></a>

The following procedure shows how to view an S3 Storage Lens dashboard in the S3 console\. For use\-case based walkthroughs that show how to use your S3 Storage Lens dashboard to optimize your storage costs, implement data\-protection and security best practices, and improve the performance of applications that access your S3 buckets, see [Amazon S3 Storage Lens metrics use cases](storage-lens-use-cases.md)\.

**Note**  
You can't use your account's root user credentials to view Amazon S3 Storage Lens dashboards\. To access S3 Storage Lens dashboards, you must grant the required AWS Identity and Access Management \(IAM\) permissions to a new or existing IAM user\. Then, sign in with those user credentials to access S3 Storage Lens dashboards\. For more information, see [Amazon S3 Storage Lens permissions](storage_lens_iam_permissions.md)\.

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the dashboard that you want to view\.

   Your dashboard opens in S3 Storage Lens\. The **Snapshot for *date*** section shows the latest date that S3 Storage Lens has collected metrics for\. Your dashboard always loads the latest date that has metrics available\.

1. \(Optional\) To change the date for your S3 Storage Lens dashboard, in the top\-right date selector, choose a new date\.

1. \(Optional\) To apply temporary filters to further limit the scope of your dashboard data, do the following:

   1. Choose **Filters**\.

      By default, all **Accounts**, **AWS Regions**, **Storage classes**, and **Buckets** are selected for your dashboard\.

   1. To filter by specific accounts, Regions, storage classes, or buckets, clear **Select all**, and choose the options to filter by\.

   1. To update a filter, choose **Apply**\.

   1. To remove all filters and return to the original selection of all accounts, Regions, storage classes, and buckets, choose **Reset**\.

1. In any section in your S3 Storage Lens dashboard, to see data for a specific metric, for **Metric**, choose the metric name\.

1. In any chart or visualization in your S3 Storage Lens dashboard, you can drill down into deeper levels of aggregation by using the **AWS Region**, **Account**, **Storage class**, or **Bucket** tabs\. For an example, see [Uncover cold Amazon S3 buckets](storage-lens-optimize-storage.md#uncover-cold-buckets)\.

## Understanding your S3 Storage Lens dashboard<a name="storage_lens_console_viewing_dashboard"></a>

Your S3 Storage Lens dashboard has a primary **Overview** tab, and up to five additional tabs that represent each aggregation level:
+ **Account** \(for organization\-level dashboards only\)
+ **Region**
+ **Storage class**
+ **Bucket**
+ **Prefix** \(available if you enable advanced metrics and recommendations in your dashboard configuration\)

On the **Overview** tab, your dashboard data is aggregated into three different sections: **Snapshot for *date***, **Trends and distributions**, and **Top N overview**\. 

For more information about your S3 Storage Lens dashboard, see the following sections\.

### Snapshot<a name="storage-lens-snapshot"></a>

The **Snapshot for *date*** section shows summary metrics that S3 Storage Lens has aggregated for the date selected\. These summary metrics include the following metrics:
+ Total storage – The total storage bytes\.
+ Object count – The total object count\.
+ Average object size – The average object size\.
+ Accounts – The number of accounts whose storage is in scope\. This value is **1** unless you are using AWS Organizations and your S3 Storage Lens has trusted access with a valid service\-linked role\. For more information, see [Using service\-linked roles for Amazon S3 Storage Lens](using-service-linked-roles.md)\. 
+ Buckets – The total number of buckets in active usage with storage > 0 bytes\.

**Metric data**  
For each metric that appears in the snapshot, you can see the following data:
+ Metric name – The name of the metric\.
+ Metric category – The category that the metric is organized into\.
+ Total for *date* – The total count for the date selected\.
+ % change – The percentage change from the last snapshot date\.
+ 30\-day trend – A trend\-line showing the changes for the metric over a 30\-day period\.
+ Recommendation – A contextual recommendation based on the data that's provided in the snapshot\. Recommendations are available with advanced metrics and recommendations\. For more information, see [Recommendations](storage_lens_basics_metrics_recommendations.md#storage_lens_basics_recommendations)\.

**Metrics categories**  
You can optionally update your dashboard **Snapshot for *date*** section to display metrics for other categories\. If you want to see snapshot data for additional metrics, you can choose from the following **Metrics categories**:
+ Cost optimization 
+ Data protection
+ Access management
+ Performance
+ Events
+ Activity \(available with advanced metrics\)

The **Snapshot for *date*** section displays only a selection of metrics for each category\. To see all metrics for a specific category, choose the metric in the **Trends and distributions** or **Top N overview** sections\. For more information about metric categories, see [Metrics categories](storage_lens_basics_metrics_recommendations.md#storage_lens_basics_metrics_types)\. For a complete list of S3 Storage Lens metrics, see [Amazon S3 Storage Lens metrics glossary](storage_lens_metrics_glossary.md)\.

### Trends and distributions<a name="storage-lens-trends"></a>

The second section of the **Overview** tab is **Trends and distributions**\. In the **Trends and distributions** section, you can choose two metrics to compare over a date range that you define\. The **Trends and distributions** section shows the relationship between two metrics over time\. This section displays charts that you can use to see the **Storage class** and **Region** distribution between the two trends that you are tracking\. You can optionally drill down into a data point in one of the charts for deeper analysis\.

 For a walkthrough that uses the **Trends and distributions** section, see [Identify buckets that don't use server\-side encryption with AWS KMS for default encryption \(SSE\-KMS\)](storage-lens-data-protection.md#storage-lens-sse-kms)\.

### Top N overview<a name="storage-lens-top-n"></a>

The third section of the S3 Storage Lens dashboard is **Top N overview** \(sorted in ascending or descending order\)\. This section displays your select metrics across the top number of Regions, buckets, or accounts if you enabled S3 Storage Lens to work with AWS Organizations\.

For a walkthrough that uses the **Top N overview** section, see [Identify your largest S3 buckets](storage-lens-optimize-storage.md#identify-largest-s3-buckets)\.

### Drill down and analyze by options<a name="storage-lens-drill-down"></a>

To provide a fluid experience for analysis, the S3 Storage Lens dashboard provides a **Drill down** action menu, which appears when you choose any chart value\. Choose any chart value to see the associated metrics values, and choose from two options in the box that appears:
+ The **Drill down** action applies the selected value as a filter across all tabs of your dashboard\. You can then drill down into that value for deeper analysis\.
+ The **Analyze by** action takes you to the selected dimension tab and applies that value as a filter\. These tabs include **AWS Region**, **Account**, **Bucket**, **Storage class**, and **Prefix** \(for dashboards that have advanced metrics and recommendations enabled and prefix aggregation selected\)\. You can then view that value in the context of the new dimension for deeper analysis\.

The **Drill down** and **Analyze by** actions might not appear if the outcome would yield illogical results or would not have any value\. Both the **Drill down** and **Analyze by** actions result in filters being applied on top of any existing filters across all tabs of the dashboard\. If necessary, you can remove the filters, or use the **Reset** option to remove all filters\.

### Tabs<a name="storage-lens-dimension-tabs"></a>

The dimension\-level tabs provide a detailed view of all values within a particular dimension\. For example, the **AWS Region** tab shows metrics for all AWS Regions, and the **Bucket** tab shows metrics for all buckets\. Each dimension tab contains an identical layout consisting of four sections:
+ A trend chart displaying your top *N* items within the dimension over the last 30 days for the selected metric\. By default, this chart displays the top 10 items, but you can increase it to any number that you want\.
+ A histogram chart shows a vertical bar chart for the selected date and metric\. If you have a large number of items to display in this chart, you might need to scroll horizontally\.
+ The bubble analysis chart plots all items within the dimension by representing the first metric on the x axis, a second metric on the y axis, and the third metric by the size of the bubble\. 
+ The metric grid view contains each item in the dimension listed in rows\. The columns represent each available metric, arranged in metrics category tabs for easier navigation\. 