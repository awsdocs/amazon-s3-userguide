# Disabling or deleting Amazon S3 Storage Lens dashboards<a name="storage_lens_console_disabling_deleting"></a>

Amazon S3 Storage Lens aggregates your usage and activity metrics and displays the information in the account snapshot on the Amazon S3 console home \(**Buckets**\) page, interactive dashboards, or through a metrics export that you can download in CSV or Parquet format\. You can use the dashboard to visualize insights and trends, flag outliers, and receive recommendations for optimizing storage costs and applying data protection best practices\. You can use S3 Storage Lens through the AWS Management Console, AWS CLI, AWS SDKs, or REST API\.



 The Amazon S3 Storage Lens default dashboard is **default\-account\-dashboard**\. This dashboard is preconfigured by Amazon S3 to help you visualize summarized insights and trends for your entire account’s aggregated storage usage and activity metrics on the console\. You can't modify its configuration scope, but you can upgrade the metrics selection from the Free Metrics to the paid Advanced Metrics and Recommendations, configure the optional metrics export, or even disable it\. The default dashboard cannot be deleted\. 

You can delete or disable an Amazon S3 Storage Lens dashboard from the Amazon S3 console\. Disabling or deleting a dashboard prevents it from generating metrics in the future\. A disabled dashboard still retains its configuration information, so it can be easily resumed when re\-enabled\. A disabled dashboard retains its historical data until it's no longer available for queries\. 

Data for Free Metrics selections is available for queries for 14 days, and data for Advanced Metrics and Recommendations selections is available for queries for 15 months\.

**Topics**
+ [Disabling an Amazon S3 Storage Lens dashboard](storage_lens_console_disabling.md)
+ [Deleting an Amazon S3 Storage Lens dashboard](storage_lens_console_deleting.md)