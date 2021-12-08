# Assessing your storage activity and usage with Amazon S3 Storage Lens<a name="storage_lens"></a>

Amazon S3 Storage Lens aggregates your usage and activity metrics and displays the information in the account snapshot on the Amazon S3 console home \(**Buckets**\) page, interactive dashboards, or through a metrics export that you can download in CSV or Parquet format\. You can use the dashboard to visualize insights and trends, flag outliers, and receive recommendations for optimizing storage costs and applying data protection best practices\. You can use S3 Storage Lens through the AWS Management Console, AWS CLI, AWS SDKs, or REST API\.

**How S3 Storage Lens works**  
Amazon S3 Storage Lens provides a single view of usage and activity across your Amazon S3 storage\. It has drilldown options to generate insights at the organization, account, bucket, object, or even prefix level\. It analyzes storage metrics to deliver contextual recommendations to help you optimize storage costs and apply best practices for protecting your data\.

You can use S3 Storage Lens to generate summary insights, such as finding out how much storage you have across your entire organization, or which are the fastest growing buckets and prefixes\. Identify outliers in your storage metrics, and then drill down to further investigate the source of the spike in usage or activity\. 

You can assess your storage based on S3 best practices, such as analyzing the percentage of your buckets that have encryption or S3 Object Lock enabled\. And you can identify potential cost savings opportunities, for example, by analyzing your request activity per bucket to find buckets where objects could be transitioned to a lower\-cost storage class\. For more information about S3 Storage Lens concepts and terminology, see [Understanding Amazon S3 Storage Lens](storage_lens_basics_metrics_recommendations.md)\.

**Default dashboard**  
On the [S3 console](https://console.aws.amazon.com/s3), S3 Storage Lens provides an interactive *default dashboard* that is updated daily\. Metrics from this dashboard are also summarized in your account snapshot on the S3 console home \(**Buckets**\) page\. You can create other dashboards and scope them by account \(for AWS Organizations users\), AWS Regions, and S3 buckets to provide [usage metrics](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage_lens_basics_metrics_recommendations.html#storage_lens_basics_metrics_types) for free\. For an additional charge, you can upgrade to receive *advanced metrics and recommendations*\. These include usage metrics with prefix\-level aggregation, activity metrics aggregated by bucket, and contextual recommendations \(available only in the Amazon S3 console\)\. For information about working with your S3 Storage Lens dashboard, see [Using Amazon S3 Storage Lens on the console](storage_lens_console.md)\.

**Metrics export**  
In addition to viewing the dashboard on the S3 console, you can export metrics in CSV or Parquet format to an S3 bucket of your choice for further analysis\. For more information, see [Viewing Amazon S3 Storage Lens metrics using a data export](storage_lens_view_metrics_export.md)\.

**Amazon CloudWatch publishing option**  
You can publish S3 Storage Lens usage and activity metrics to Amazon CloudWatch to create a unified view of your operational health in CloudWatch [dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html)\. You can also use CloudWatch features like alarms and triggered actions, metric math, and anomaly detection to monitor and take action on S3 Storage Lens metrics\. In addition, the CloudWatch APIs enable applications, including third\-party providers, to access your S3 Storage Lens metrics\. The CloudWatch publishing option is available for dashboards upgraded to S3 Storage Lens *advanced metrics and recommendations*\. For more information about support for S3 Storage Lens metrics in CloudWatch, see [Monitor S3 Storage Lens metrics in CloudWatch](storage_lens_view_metrics_cloudwatch.md)\.

For more information about using S3 Storage Lens, see [Working with Amazon S3 Storage Lens using the console and API](S3LensExamples.md)\. For information about S3 Storage Lens pricing, see [Amazon S3 pricing](http://aws.amazon.com/s3/pricing)\.

**Topics**
+ [Understanding Amazon S3 Storage Lens](storage_lens_basics_metrics_recommendations.md)
+ [Using Amazon S3 Storage Lens with AWS Organizations](storage_lens_with_organizations.md)
+ [Setting permissions to use Amazon S3 Storage Lens](storage_lens_iam_permissions.md)
+ [Viewing storage usage and activity metrics with Amazon S3 Storage Lens](storage_lens_view_metrics.md)
+ [Using Amazon S3 Storage Lens to optimize your storage costs](storage-lens-optimize-storage.md)
+ [Amazon S3 Storage Lens metrics glossary](storage_lens_metrics_glossary.md)
+ [Working with Amazon S3 Storage Lens using the console and API](S3LensExamples.md)