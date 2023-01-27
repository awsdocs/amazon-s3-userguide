# Assessing your storage activity and usage with Amazon S3 Storage Lens<a name="storage_lens"></a>

Amazon S3 Storage Lens is a cloud\-storage analytics feature that you can use to gain organization\-wide visibility into object\-storage usage and activity\. S3 Storage Lens also analyzes metrics to deliver contextual recommendations that you can use to optimize storage costs and apply best practices for protecting your data\. 

You can use S3 Storage Lens metrics to generate summary insights, such as finding out how much storage you have across your entire organization or which are the fastest\-growing buckets and prefixes\. You can also use S3 Storage Lens metrics to identify cost\-optimization opportunities, implement data\-protection and access\-management best practices, and improve the performance of application workloads\. For example, you can identify buckets that don't have S3 Lifecycle rules to abort incomplete multipart uploads that are more than 7 days old\. You can also identify buckets that aren't following data\-protection best practices, such as using S3 Replication or S3 Versioning\. 

S3 Storage Lens aggregates your metrics and displays the information in the **Account snapshot** section on the Amazon S3 console **Buckets** page\. S3 Storage Lens also provides an interactive dashboard that you can use to visualize insights and trends, flag outliers, and receive recommendations for optimizing storage costs and applying data\-protection best practices\. Your dashboard has drill\-down options to generate insights at the organization, account, bucket, object, or prefix level\. You can also send a once\-daily metrics export in CSV or Parquet format to an S3 bucket\. 

![\[The Snapshot for date section in the S3 Storage Lens dashboard.\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/storage-lens-dashboard.png)

## S3 Storage Lens metrics and features<a name="storage-lens-dashboards-intro"></a>

S3 Storage Lens provides an interactive *default dashboard* that is updated daily\. S3 Storage Lens preconfigures this dashboard to visualize the summarized insights and trends for your entire account and updates them daily in the S3 console\. Metrics from this dashboard are also summarized in your account snapshot on the **Buckets** page\. For more information, see [Default dashboard](storage_lens_basics_metrics_recommendations.md#storage_lens_basics_default_dashboard)\.

To create other dashboards and scope them by AWS Regions, S3 buckets, or accounts \(for AWS Organizations\), you create an S3 Storage Lens dashboard configuration\. You can create and manage S3 Storage Lens dashboard configurations by using the AWS Management Console, AWS Command Line Interface \(AWS CLI\), AWS SDKs, or REST API\. When you create or edit an S3 Storage Lens dashboard, you define your dashboard scope and metrics selection\. 

S3 Storage Lens offers free metrics and advanced metrics and recommendations, which you can upgrade to for an additional charge\. With advanced metrics and recommendations, you can access additional metrics and features for gaining insight into your storage\. These features include advanced metric categories, prefix aggregation, contextual recommendations, and Amazon CloudWatch publishing\. Prefix aggregation and contextual recommendations are available only in the Amazon S3 console\. For information about S3 Storage Lens pricing, see [Amazon S3 pricing](http://aws.amazon.com/s3/pricing)\.

**Metrics categories**  
Within the free and advanced tiers, metrics are organized into categories that align with key use cases, such as cost optimization and data protection\. Free metrics include summary, cost optimization, data protection, access management, performance, and event metrics\. When you upgrade to advanced metrics and recommendations, you can enable advanced cost\-optimization and data\-protection metrics\. You can use these advanced metrics to further reduce your S3 storage costs and improve your data\-protection stance\. You can also enable activity metrics and detailed status\-code metrics to improve the performance of application workloads that are accessing your S3 buckets\. For more information about the free and advanced metrics categories, see [Metrics selection](storage_lens_basics_metrics_recommendations.md#storage_lens_basics_metrics_selection)\.

You can assess your storage based on S3 best practices, such as analyzing the percentage of your buckets that have encryption or S3 Object Lock or S3 Versioning enabled\. You can also identify potential cost\-savings opportunities\. For example, you can use S3 Lifecycle rule count metrics to identify buckets that are missing lifecycle expiration or transition rules\. You can also analyze your request activity per bucket to find buckets where objects could be transitioned to a lower\-cost storage class\. For more information, see [Amazon S3 Storage Lens metrics use cases](storage-lens-use-cases.md)\.

**Metrics export**  
In addition to viewing the dashboard on the S3 console, you can export metrics in CSV or Parquet format to an S3 bucket for further analysis with the analytics tool of your choice\. For more information, see [Viewing Amazon S3 Storage Lens metrics using a data export](storage_lens_view_metrics_export.md)\.

**Amazon CloudWatch publishing**  
You can publish S3 Storage Lens usage and activity metrics to Amazon CloudWatch to create a unified view of your operational health in CloudWatch [dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html)\. You can also use CloudWatch features, such as alarms and triggered actions, metric math, and anomaly detection, to monitor and take action on S3 Storage Lens metrics\. In addition, CloudWatch API operations enable applications, including third\-party providers, to access your S3 Storage Lens metrics\. The CloudWatch publishing option is available for dashboards that are upgraded to S3 Storage Lens advanced metrics and recommendations\. For more information about support for S3 Storage Lens metrics in CloudWatch, see [Monitor S3 Storage Lens metrics in CloudWatch](storage_lens_view_metrics_cloudwatch.md)\.

For more information about using S3 Storage Lens, see the following topics\.

**Topics**
+ [S3 Storage Lens metrics and features](#storage-lens-dashboards-intro)
+ [Understanding Amazon S3 Storage Lens](storage_lens_basics_metrics_recommendations.md)
+ [Using Amazon S3 Storage Lens with AWS Organizations](storage_lens_with_organizations.md)
+ [Amazon S3 Storage Lens permissions](storage_lens_iam_permissions.md)
+ [Viewing metrics with Amazon S3 Storage Lens](storage_lens_view_metrics.md)
+ [Amazon S3 Storage Lens metrics use cases](storage-lens-use-cases.md)
+ [Amazon S3 Storage Lens metrics glossary](storage_lens_metrics_glossary.md)
+ [Working with Amazon S3 Storage Lens using the console and API](S3LensExamples.md)