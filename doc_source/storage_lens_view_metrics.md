# Viewing metrics with Amazon S3 Storage Lens<a name="storage_lens_view_metrics"></a>

S3 Storage Lens aggregates your metrics and displays the information in the **Account snapshot** section on the Amazon S3 console **Buckets** page\. S3 Storage Lens also provides an interactive dashboard that you can use to visualize insights and trends, flag outliers, and receive recommendations for optimizing storage costs and applying data\-protection best practices\. Your dashboard has drill\-down options to generate insights at the organization, account, bucket, object, or prefix level\. You can also send a once\-daily metrics export in CSV or Parquet format to an S3 bucket\.

By default, all dashboards are configured with free metrics, which include metrics that you can use to understand usage and activity across your S3 storage, optimize your storage costs, and implement data\-protection and access\-management best practices\. Free metrics are aggregated down to the bucket level\. With free metrics, data is available for queries for 14 days\.

Advanced metrics and recommendations include the following additional features that you can use to gain further insight into usage and activity across your storage and best practices for optimizing your storage:
+ Contextual recommendations \(available only in the dashboard\)
+ Advanced metrics \(including activity metrics aggregated by bucket\)
+ Prefix aggregation
+ Amazon CloudWatch publishing

Advanced metrics data is available for queries for 15 months\. There are additional charges for using S3 Storage Lens with advanced metrics\. For more information, see [Amazon S3 pricing](http://aws.amazon.com/s3/pricing)\. For more information about free and advanced metrics, see [Metrics selection](storage_lens_basics_metrics_recommendations.md#storage_lens_basics_metrics_selection)\.

**Topics**
+ [Viewing S3 Storage Lens metrics on the dashboards](storage_lens_view_metrics_dashboard.md)
+ [Viewing Amazon S3 Storage Lens metrics using a data export](storage_lens_view_metrics_export.md)
+ [Monitor S3 Storage Lens metrics in CloudWatch](storage_lens_view_metrics_cloudwatch.md)