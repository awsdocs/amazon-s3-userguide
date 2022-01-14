# Working with S3 Storage Lens metrics in CloudWatch<a name="storage-lens-cloudwatch-monitoring-cloudwatch"></a>

You can publish S3 Storage Lens usage and activity metrics to Amazon CloudWatch to create a unified view of your operational health in CloudWatch [dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html)\. You can also use CloudWatch features, like alarms and triggered actions, metric math, and anomaly detection, to monitor and take action on S3 Storage Lens metrics\. In addition, the CloudWatch APIs enable applications, including third\-party providers, to access your S3 Storage Lens metrics\. For more information about CloudWatch features, see the *[Amazon CloudWatch User Guide](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_concepts.html)*\.

You can enable the CloudWatch publishing option for new or existing dashboard configurations using the Amazon S3 console, Amazon S3 REST APIs, AWS CLI, and AWS SDKs\. The CloudWatch publishing option is available for dashboards upgraded to S3 Storage Lens *advanced metrics and recommendations\. *For S3 Storage Lens* advanced metrics and recommendations * pricing, see [Amazon S3 pricing](http://aws.amazon.com/s3/pricing/)\. No additional CloudWatch metrics publishing charges apply; however, other CloudWatch charges such as dashboards, alarms, and API are applicable\. For more information, see [Amazon CloudWatch Pricing](http://aws.amazon.com/cloudwatch/pricing/)\. 

S3 Storage Lens metrics are published to CloudWatch in the account that owns the S3 Storage Lens configuration\. After you enable the CloudWatch publishing option within advanced metrics and recommendations, you can access organization, account, and bucket\-level usage and activity metrics in CloudWatch\. Prefix\-level metrics are not available in CloudWatch\.

**Note**  
S3 Storage Lens metrics are daily metrics and are published to CloudWatch once per day\. When you query S3 Storage Lens metrics in CloudWatch, the period for the query must be 1 day \(86400 seconds\)\. After your daily S3 Storage Lens metrics appear in your S3 Storage Lens dashboard in the Amazon S3 console, it can take a few hours for these same metrics to appear in CloudWatch\. When you enable the CloudWatch publishing option for S3 Storage Lens metrics for the first time, it can take up to 24 hours for your metrics to publish to CloudWatch\.   
Currently, S3 Storage Lens metrics cannot be consumed via CloudWatch streams\. 

For more information about working with S3 Storage Lens metrics in CloudWatch, see the following topics\.

**Topics**
+ [Working with CloudWatch dashboards](#storage-lens-cloudwatch-monitoring-cloudwatch-dashboards)
+ [Setting alarms, triggering actions, and using anomaly detection](#storage-lens-cloudwatch-monitoring-cloudwatch-alarms)
+ [Filtering metrics using dimensions](#storage-lens-cloudwatch-monitoring-cloudwatch-dimensions)
+ [Calculating new metrics with metric math](#storage-lens-cloudwatch-monitoring-cloudwatch-metric-math)
+ [Using search expressions in graphs](#storage-lens-cloudwatch-monitoring-cloudwatch-search-expressions)

## Working with CloudWatch dashboards<a name="storage-lens-cloudwatch-monitoring-cloudwatch-dashboards"></a>

You can use CloudWatch dashboards to monitor S3 Storage Lens metrics alongside other application metrics and create a unified view of your operational health\. Dashboards are customizable home pages in the CloudWatch console that you can use to monitor your resources in a single view\. 

CloudWatch has broad permissions control that doesn’t support limiting access to a specific set of metrics or dimensions\. Users in your account or organization who have access to CloudWatch will have access to metrics for all S3 Storage Lens configurations where the CloudWatch support option is enabled\. You can't manage permissions for specific dashboards as you can in S3 Storage Lens\. For more information about CloudWatch permissions, see [Managing access permissions to your CloudWatch resources](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/iam-access-control-overview-cw.html) in the *Amazon CloudWatch User Guide*\.

For more information about using CloudWatch dashboards and configuring permissions, see [Using Amazon CloudWatch dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html) and [Sharing CloudWatch dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch-dashboard-sharing.html) in the *Amazon CloudWatch User Guide*\.

## Setting alarms, triggering actions, and using anomaly detection<a name="storage-lens-cloudwatch-monitoring-cloudwatch-alarms"></a>

You can configure CloudWatch alarms that watch S3 Storage Lens metrics in CloudWatch and take action when a threshold is breached\. For example, you can configure an alarm that sends an Amazon SNS notification when Incomplete Multipart Upload Bytes exceeds 1 GB for three consecutive days\.

You can also enable anomaly detection to continuously analyze your S3 Storage Lens metrics, determine normal baselines, and surface anomalies\. You can create an anomaly detection alarm based on a metric's expected value\. For example, you can monitor anomalies for Object Lock Enabled Bytes to detect unauthorized removal of Object Lock settings\.

For more information and examples, see [Using Amazon CloudWatch alarms](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/AlarmThatSendsEmail.html) and [Creating an alarm from a metric on a graph](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/create_alarm_metric_graph.html) in the *Amazon CloudWatch User Guide*\.

## Filtering metrics using dimensions<a name="storage-lens-cloudwatch-monitoring-cloudwatch-dimensions"></a>

You can use dimensions to filter S3 Storage Lens metrics in the CloudWatch console\. For example, you can filter by `configuration_id`, `aws_account_number`, `aws_region`, `bucket_name`, and more\.

S3 Storage Lens supports multiple dashboard configurations per account\. This means that different configurations can include the same bucket\. When these metrics are published the CloudWatch, the bucket will have duplicate metrics within CloudWatch\. To view metrics only for a specific S3 Storage Lens configuration in CloudWatch, you can use the `configuration_id` dimension\. When you filter by `configuration_ID`, you only see metrics associated with the configuration that you identify\.

For more information about filtering by configuration ID, see the following procedure, or see [Searching for available metrics](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/finding_metrics_with_cloudwatch.html) in the *Amazon CloudWatch User Guide*\.

## Calculating new metrics with metric math<a name="storage-lens-cloudwatch-monitoring-cloudwatch-metric-math"></a>

You can use metric math to query multiple S3 Storage Lens metrics and use math expressions to create new time series based on these metrics\. For example, you can create a new metric for unencrypted objects by subtracting Encrypted Objects from Object Count\. You can also create a metric to get the average object size by dividing StorageBytes by ObjectCount or the number bytes accessed on one day by dividing BytesDownloaded by StorageBytes\.

For more information, see [Using metric math](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/using-metric-math.html) in the *Amazon CloudWatch User Guide*\.

## Using search expressions in graphs<a name="storage-lens-cloudwatch-monitoring-cloudwatch-search-expressions"></a>

With S3 Storage Lens metrics, you can create a search expression for all metrics called **IncompleteMultipartUploadStorageBytes** and add `SUM` to the expression\. With this search expression, you can see your total Incomplete MPU bytes across all dimensions of your storage in a single metric\.

This example shows the syntax that you would use to create a search expression for all metrics called **IncompleteMultipartUploadStorageBytes**\.

```
SUM(SEARCH('{AWS/S3/Storage-Lens,aws_account_number,aws_region,configuration_id,metrics_version,record_type,storage_class} MetricName="IncompleteMultipartUploadStorageBytes"', 'Average',86400))
```

For more information about this syntax, see [CloudWatch search expression syntax](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/search-expression-syntax.html) in the *Amazon CloudWatch User Guide*\. To create a CloudWatch graph with a search expression, see [Creating a CloudWatch graph with a search expression](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/create-search-expression.html)in the *Amazon CloudWatch User Guide*\.