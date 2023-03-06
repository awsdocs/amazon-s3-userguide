# Understanding Amazon S3 Storage Lens<a name="storage_lens_basics_metrics_recommendations"></a>

**Important**  
Amazon S3 now applies server\-side encryption with Amazon S3 managed keys \(SSE\-S3\) as the base level of encryption for every bucket in Amazon S3\. Starting January 5, 2023, all new object uploads to Amazon S3 are automatically encrypted at no additional cost and with no impact on performance\. The automatic encryption status for S3 bucket default encryption configuration and for new object uploads is available in AWS CloudTrail logs, S3 Inventory, S3 Storage Lens, the Amazon S3 console, and as an additional Amazon S3 API response header in the AWS Command Line Interface and AWS SDKs\. For more information, see [Default encryption FAQ](https://docs.aws.amazon.com/AmazonS3/latest/userguide/default-encryption-faq.html)\.

Amazon S3 Storage Lens is a cloud\-storage analytics feature that you can use to gain organization\-wide visibility into object\-storage usage and activity\. You can use S3 Storage Lens metrics to generate summary insights, such as finding out how much storage you have across your entire organization or which are the fastest\-growing buckets and prefixes\. You can also use S3 Storage Lens metrics to identify cost\-optimization opportunities, implement data\-protection and security best practices, and improve the performance of application workloads\. For example, you can identify buckets that don't have S3 Lifecycle rules to expire incomplete multipart uploads that are more than 7 days old\. You can also identify buckets that aren't following data\-protection best practices, such as using S3 Replication or S3 Versioning\. S3 Storage Lens also analyzes metrics to deliver contextual recommendations that you can use to optimize storage costs and apply best practices for protecting your data\. 

S3 Storage Lens aggregates your metrics and displays the information in the **Account snapshot** section on the Amazon S3 console **Buckets** page\. S3 Storage Lens also provides an interactive dashboard that you can use to visualize insights and trends, flag outliers, and receive recommendations for optimizing storage costs and applying data\-protection best practices\. Your dashboard has drill\-down options to generate insights at the organization, account, bucket, object, or prefix level\. You can also send a once\-daily metrics export in CSV or Parquet format to an S3 bucket\. You can create and manage S3 Storage Lens dashboards by using the AWS Management Console, AWS Command Line Interface \(AWS CLI\), AWS SDKs, or REST API\. 

## S3 Storage Lens concepts and terminology<a name="storage_lens_basics"></a>

This section contains the terminology and concepts that are essential for successfully understanding and using Amazon S3 Storage Lens\.

**Topics**
+ [Dashboard configuration](#storage_lens_basics_configuration)
+ [Default dashboard](#storage_lens_basics_default_dashboard)
+ [Dashboards](#storage_lens_basics_dashboards)
+ [Account snapshot](#storage_lens_basics_account_snapshot)
+ [Metrics export](#storage_lens_basics_metrics_export)
+ [Home Region](#storage_lens_basics_home_region)
+ [Retention period](#storage_lens_basics_data_queries)
+ [Metrics categories](#storage_lens_basics_metrics_types)
+ [Recommendations](#storage_lens_basics_recommendations)
+ [Metrics selection](#storage_lens_basics_metrics_selection)
+ [S3 Storage Lens and AWS Organizations](#storage_lens_basics_organizations)

### Dashboard configuration<a name="storage_lens_basics_configuration"></a>

S3 Storage Lens requires a dashboard configuration that contains the properties required to aggregate metrics on your behalf for a single dashboard or export\. When you create a configuration, you choose the dashboard name and the home Region, which you can't change after you create the dashboard\. You can optionally add tags and configure a metrics export in CSV or Parquet format\. 

In the dashboard configuration, you also define the dashboard scope and the metrics selection\. The scope can include all the storage for your organization account or sections that are filtered by Region, bucket, and account\. When you configure the metrics selection, you choose between free metrics and advanced metrics and recommendations, which you can upgrade to for an additional charge\. With advanced metrics and recommendations, you can access additional metrics and features\. These features include advanced metric categories, prefix\-level aggregation, contextual recommendations, and Amazon CloudWatch publishing\. For information about S3 Storage Lens pricing, see [Amazon S3 pricing](http://aws.amazon.com/s3/pricing)\.

### Default dashboard<a name="storage_lens_basics_default_dashboard"></a>

The S3 Storage Lens default dashboard on the console is named **default\-account\-dashboard**\. S3 preconfigures this dashboard to visualize the summarized insights and trends for your entire account and updates them daily in the S3 console\. You can't modify the configuration scope of the default dashboard, but you can upgrade the metrics selection from free metrics to advanced metrics and recommendations\. You can configure the optional metrics export or even disable the dashboard\. However, you can't delete the default dashboard\.

**Note**  
If you disable your default dashboard, it is no longer updated\. You'll no longer receive any new daily metrics in S3 Storage Lens or in the account snapshot on the S3 **Buckets** page\. You can still see historic data in the dashboard until the 14\-day period for data queries expires\. This period is 15 months if you've enabled advanced metrics and recommendations\. To access historic data, you can re\-enable the dashboard within the expiration period\.

### Dashboards<a name="storage_lens_basics_dashboards"></a>

You can create additional S3 Storage Lens dashboards and scope them by AWS Regions, S3 buckets, or accounts \(for AWS Organizations\)\. When you create or edit a S3 Storage Lens dashboard, you define your dashboard scope and metrics selection\. S3 Storage Lens offers free metrics and advanced metrics and recommendations, which you can upgrade to for an additional charge\. With advanced metrics and recommendations, you can access additional metrics and features for gaining insight into your storage\. These include advanced metric categories, prefix\-level aggregation, contextual recommendations, and Amazon CloudWatch publishing\. For information about S3 Storage Lens pricing, see [Amazon S3 pricing](http://aws.amazon.com/s3/pricing)\.

You can also disable or delete dashboards\. If you disable a dashboard, it is no longer updated, and you will no longer receive any new daily metrics\. You can still see historic data until the 14\-day expiration period\. If you enabled advanced metrics and recommendations for that dashboard, this period is 15 months\. To access historic data, you can re\-enable the dashboard within the expiration period\. 

If you delete your dashboard, you lose all your dashboard configuration settings\. You will no longer receive any new daily metrics, and you also lose access to the historical data associated with that dashboard\. If you want to access the historic data for a deleted dashboard, you must create another dashboard with the same name in the same home Region\.

**Note**  
You can use S3 Storage Lens to create up to 50 dashboards per home Region\.
Organization\-level dashboards can be limited only to a Regional scope\.

### Account snapshot<a name="storage_lens_basics_account_snapshot"></a>

The S3 Storage Lens **Account snapshot** summarizes metrics from your default dashboard and displays your total storage, object count, and average object size on the S3 console **Buckets** page\. This account snapshot gives you quick access to insights about your storage without having to leave the **Buckets** page\. The account snapshot also provides one\-click access to your interactive S3 Storage Lens dashboard\. 

You can use your dashboard to visualize insights and trends, flag outliers, and receive recommendations for optimizing storage costs and applying data\-protection best practices\. Your dashboard has drill\-down options to generate insights at the organization, account, bucket, object, or prefix level\. You can also send a once\-daily metrics export to an S3 bucket in CSV or Parquet format\.

You can't modify the dashboard scope of the **default\-account dashboard** because it's linked to the **Account snapshot**\. However, you can upgrade the metrics selection in your **default\-account\-dashboard** from free metrics to paid advanced metrics and recommendations\. After upgrading, you can then display all requests, bytes uploaded, and bytes downloaded in the S3 Storage Lens **Account snapshot**\. 

**Note**  
If you disable your default dashboard, your **Account snapshot** is no longer updated\. To continue displaying metrics in the **Account snapshot**, you can re\-enable the **default\-account\-dashboard**\.

### Metrics export<a name="storage_lens_basics_metrics_export"></a>

An S3 Storage Lens metrics export is a file that contains all the metrics identified in your S3 Storage Lens configuration\. This information is generated daily in CSV or Parquet format and is sent to an S3 bucket\. You can use the metrics export for further analysis by using the metrics tool of your choice\. The S3 bucket for your metrics export must be in the same Region as your S3 Storage Lens configuration\. You can generate an S3 Storage Lens metrics export from the S3 console by editing your dashboard configuration\. You can also configure a metrics export by using the AWS CLI and AWS SDKs\.

### Home Region<a name="storage_lens_basics_home_region"></a>

The home Region is the AWS Region where all S3 Storage Lens metrics for a given dashboard configuration are stored\. You must choose a home Region when you create your S3 Storage Lens dashboard configuration\. After you choose a home Region, you can't change it\.

**Note**  
You can choose one of the following Regions as your home Region:  
US East \(N\. Virginia\) – `us-east-1`
US East \(Ohio\) – `us-east-2`
US West \(N\. California\) – `us-west-1`
US West \(Oregon\) – `us-west-2`
Asia Pacific \(Mumbai\) – `ap-south-1`
Asia Pacific \(Seoul\) – `ap-northeast-2`
Asia Pacific \(Singapore\) – `ap-southeast-1`
Asia Pacific \(Sydney\) – `ap-southeast-2`
Asia Pacific \(Tokyo\) – `ap-northeast-1`
Canada \(Central\) – `ca-central-1`
China \(Beijing\) – `cn-north-1`
China \(Ningxia\) – `cn-northwest-1`
Europe \(Frankfurt\) – `eu-central-1`
Europe \(Ireland\) – `eu-west-1`
Europe \(London\) – `eu-west-2`
Europe \(Paris\) – `eu-west-3`
Europe \(Stockholm\) – `eu-north-1`
South America \(São Paulo\) – `sa-east-1`

### Retention period<a name="storage_lens_basics_data_queries"></a>

S3 Storage Lens metrics are retained so that you can see historical trends and compare differences in your storage usage and activity over time\. You can use Amazon S3 Storage Lens metrics for queries so that you can see historical trends and compare differences in your storage usage and activity over time\. 

All S3 Storage Lens metrics are retained for a period of 15 months\. However, metrics are only available for queries for a specific duration, which depends on your [metrics selection](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage_lens_basics_metrics_recommendations.html#storage_lens_basics_metrics_selection)\. This duration can't be modified\. Free metrics are available for queries for a 14\-day period, and advanced metrics are available for queries for a 15\-month period\.

### Metrics categories<a name="storage_lens_basics_metrics_types"></a>

Within the free and advanced tiers, S3 Storage Lens metrics are organized into categories that align with key use cases, such as cost optimization and data protection\. Free metrics include summary, cost optimization, data protection, access management, performance, and event metrics\. When you upgrade to advanced metrics and recommendations, you can enable additional cost\-optimization and data\-protection metrics that you can use to further reduce your S3 storage costs and improve your data\-protection stance\. You can also enable activity metrics and detailed status\-code metrics that you can use to improve the performance of application workflows\. 

The following list shows all of the free and advanced metric categories\. For a complete list of the individual metrics included in each category, see [Metrics glossary](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage_lens_metrics_glossary.html)\.

**Summary metrics**  
Summary metrics provide general insights about your S3 storage, including your total storage bytes and object count\. 

**Cost\-optimization metrics**  
Cost\-optimization metrics provide insights that you can use to manage and optimize your storage costs\. For example, you can identify buckets that have incomplete multipart uploads that are more than 7\-days old\.

With advanced metrics and recommendations, you can enable advanced cost\-optimization metrics\. These metrics include S3 Lifecycle rule count metrics that you can use to get per\-bucket expiration and transition S3 Lifecycle rule counts\. 

**Data\-protection metrics**  
Data\-protection metrics provide insights for data\-protection features, such as encryption and S3 Versioning\. You can use these metrics to identify buckets that are not following data\-protection best practices\. For example, you can identify buckets that are not using default encryption with AWS Key Management Service keys \(SSE\-KMS\) or S3 Versioning\.

With advanced metrics and recommendations, you can enable advanced data\-protection metrics\. These metrics include per\-bucket replication rule count metrics\.

**Access\-management metrics**  
Access\-management metrics provide insights for S3 Object Ownership\. You can use these metrics to see which Object Ownership settings your buckets use\.

**Event metrics**  
Event metrics provide insights for S3 Event Notifications\. With event metrics, you can see which buckets have S3 Event Notifications configured\.

**Performance metrics**  
Performance metrics provide insights for S3 Transfer Acceleration\. With performance metrics, you can see which buckets have Transfer Acceleration enabled\.

**Activity metrics \(advanced\)**  
If you upgrade your dashboard to advanced metrics and recommendations, you can enable activity metrics\. Activity metrics provide details about how your storage is requested \(for example, All requests, Get requests, Put requests\), bytes uploaded or downloaded, and errors\.

**Detailed status\-code metrics \(advanced\)**  
If you upgrade your dashboard to advanced metrics and recommendations, you can enable detailed status\-code metrics\. Detailed status\-code metrics provide insights for HTTP status codes, such as 403 Forbidden and 503 Service Unavailable, that you can use to troubleshoot access or performance issues\. For example, you can look at the **403 Forbidden error count** metric to identify workloads that are accessing buckets without the correct permissions applied\. 

### Recommendations<a name="storage_lens_basics_recommendations"></a>

S3 Storage Lens provides automated recommendations to help you optimize your storage\. Recommendations are placed contextually alongside relevant metrics in the S3 Storage Lens dashboard\. Historical data is not eligible for recommendations because recommendations are relevant to what is happening in the most recent period\. Recommendations appear only when they are relevant\.

S3 Storage Lens recommendations come in the following forms:
+ **Suggestions**

  Suggestions alert you to trends within your storage usage and activity that might indicate a storage\-cost optimization opportunity or a data\-protection best practice\. You can use the suggested topics in the *Amazon S3 User Guide* and the S3 Storage Lens dashboard to drill down for more details about the specific Regions, buckets, or prefixes to further assist you\.
+ **Call\-outs**

  Call\-outs are recommendations that alert you to interesting anomalies within your storage usage and activity over a period that might need further attention or monitoring\.
  + **Outlier call\-outs**

    S3 Storage Lens provides call\-outs for metrics that are outliers, based on your recent 30\-day trend\. The outlier is calculated by using a standard score, also known as a *z\-score*\. In this score, the current day's metric is subtracted from the average of the last 30 days for that metric, and then divided by the standard deviation for that metric over the last 30 days\. The resulting score is usually between \-3 and \+3\. This number represents the number of standard deviations that the current day's metric is from the mean\. 

    S3 Storage Lens considers metrics with a score >2 or <\-2 to be outliers because they are higher or lower than 95 percent of normally distributed data\. 
  + **Significant change call\-outs**

    The significant change call\-out applies to metrics that are expected to change less frequently\. Therefore, it is set to a higher sensitivity than the outlier calculation, which is typically in the range of \+/\- 20 percent versus the prior day, week, or month\.

    **Addressing call\-outs in your storage usage and activity** – If you receive a significant change call\-out, it’s not necessarily a problem and could be the result of an anticipated change in your storage\. For example, you might have recently added a large number of new objects, deleted a large number of objects, or made similar planned changes\. 

    If you see a significant change call\-out on your dashboard, take note of it and determine whether it can be explained by recent circumstances\. If not, use the S3 Storage Lens dashboard to drill down for more details to understand the specific Regions, buckets, or prefixes that are driving the fluctuation\.
+ **Reminders**

  Reminders provide insights into how Amazon S3 works\. They can help you learn more about ways to use S3 features to reduce storage costs or apply data\-protection best practices\.

### Metrics selection<a name="storage_lens_basics_metrics_selection"></a>

S3 Storage Lens offers two metrics selections that you can choose for your dashboard and export: *free metrics* and *advanced metrics and recommendations*\.
+ **Free metrics**

  S3 Storage Lens offers free metrics for all dashboards and configurations\. Free metrics contain metrics that are relevant to your storage, such as the number of buckets and the objects in your account\. Free metrics also include use\-case based metrics \(for example, cost\-optimization and data\-protection metrics\) that you can use to investigate whether your storage is configured according to S3 best practices\. All free metrics are collected daily\. Data is available for queries for 14 days\. For more information about which metrics are available with free metrics, see the [Amazon S3 Storage Lens metrics glossary](storage_lens_metrics_glossary.md)\.
+ **Advanced metrics and recommendations**

  S3 Storage Lens offers free metrics for all dashboards and configurations with the option to upgrade to advanced metrics and recommendations\. Additional charges apply\. For more information, see [Amazon S3 pricing](http://aws.amazon.com/s3/pricing/)\. 

  Advanced metrics and recommendations include all the metrics in free metrics along with additional metrics, such as advanced data\-protection and cost\-optimization metrics, activity metrics, and detailed status\-code metrics\. Advanced metrics and recommendations also provide recommendations to help you optimize your storage\. Recommendations are placed contextually alongside relevant metrics in the dashboard\. 

  Advanced metrics and recommendations includes the following features:
  + **Advanced metrics** – Generate additional metrics\. For a complete list of advanced metric categories, see [Metrics categories](#storage_lens_basics_metrics_types)\. For a complete list of metrics, see the [Amazon S3 Storage Lens metrics glossary](storage_lens_metrics_glossary.md)\.
  + **Amazon CloudWatch publishing** – Publish S3 Storage Lens metrics to CloudWatch to create a unified view of your operational health in CloudWatch [dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html)\. You can also use CloudWatch API operations and features, such as alarms and triggered actions, metric math, and anomaly detection, to monitor and take action on S3 Storage Lens metrics\. For more information, see [Monitor S3 Storage Lens metrics in CloudWatch](storage_lens_view_metrics_cloudwatch.md)\.
  + **Prefix aggregation** – Collect metrics at the prefix level\. Prefix\-level metrics are not published to CloudWatch\.

  All advanced metrics are collected daily\. Data is available for queries for 15 months\. For more information about the storage metrics that are aggregated by S3 Storage Lens, see [Amazon S3 Storage Lens metrics glossary](storage_lens_metrics_glossary.md)\.
**Note**  
Recommendations are available only when you use the S3 Storage Lens dashboard on the Amazon S3 console,

### S3 Storage Lens and AWS Organizations<a name="storage_lens_basics_organizations"></a>

AWS Organizations is an AWS service that helps you aggregate all of your AWS accounts under one organization hierarchy\. Amazon S3 Storage Lens works with AWS Organizations to provide a single view of object storage usage and activity across your Amazon S3 storage\.

For more information, see [Using Amazon S3 Storage Lens with AWS OrganizationsEnabling trusted access for S3 Storage Lens](storage_lens_with_organizations.md)\.
+ **Trusted access**

  Using your organization's management account, you must enable *trusted access* for S3 Storage Lens to aggregate storage metrics and usage data for all member accounts in your organization\. You can then create dashboards or exports for your organization by using your management account or by giving delegated administrator access to other accounts in your organization\. 

  You can disable trusted access for S3 Storage Lens at any time, which stops S3 Storage Lens from aggregating metrics for your organization\.
+ **Delegated administrator**

  You can create dashboards and metrics for S3 Storage Lens for your organization by using your AWS Organizations management account, or by giving *delegated administrator* access to other accounts in your organization\. You can deregister delegated administrators at any time\. Deregistering a delegated administrator also automatically stops all organization\-level dashboards created by that delegated administrator from aggregating new storage metrics\.

For more information, see [Amazon S3 Storage Lens and AWS Organizations](https://docs.aws.amazon.com/organizations/latest/userguide/services-that-can-integrate-s3lens.html) in the *AWS Organizations User Guide*\.

#### Amazon S3 Storage Lens service\-linked roles<a name="storage_lens_basics_service_linked_role"></a>

Along with AWS Organizations trusted access, Amazon S3 Storage Lens uses AWS Identity and Access Management \(IAM\) service\-linked roles\. A service\-linked role is a unique type of IAM role that is linked directly to S3 Storage Lens\. Service\-linked roles are predefined by S3 Storage Lens and include all the permissions that it requires to collect daily storage usage and activity metrics from member accounts in your organization\. 

For more information, see [Using service\-linked roles for Amazon S3 Storage Lens](https://docs.aws.amazon.com/AmazonS3/latest/userguide/using-service-linked-roles.html)\.