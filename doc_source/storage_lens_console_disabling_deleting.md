# Disabling or deleting Amazon S3 Storage Lens dashboards<a name="storage_lens_console_disabling_deleting"></a>

S3 Storage Lens aggregates your metrics and displays the information in the **Account snapshot** section on the Amazon S3 console **Buckets** page\. S3 Storage Lens also provides an interactive dashboard that you can use to visualize insights and trends, flag outliers, and receive recommendations for optimizing storage costs and applying data\-protection best practices\. Your dashboard has drill\-down options to generate insights at the organization, account, bucket, object, or prefix level\. You can also send a once\-daily metrics export in CSV or Parquet format to an S3 bucket\.



 The Amazon S3 Storage Lens default dashboard is **default\-account\-dashboard**\. This dashboard is preconfigured by Amazon S3 to help you visualize summarized insights and trends for your entire account's aggregated free and advanced metrics on the console\. You can't modify the default dashboard's configuration scope, but you can upgrade the metrics selection from the free metrics to the paid advanced metrics and recommendations, configure the optional metrics export, or even disable the default dashboard\. The default dashboard cannot be deleted\. 

You can delete or disable an Amazon S3 Storage Lens dashboard from the Amazon S3 console\. Disabling or deleting a dashboard prevents it from generating metrics in the future\. A disabled dashboard still retains its configuration information, so it can be easily resumed when re\-enabled\. A disabled dashboard retains its historical data until it's no longer available for queries\. 

Data for free metrics selections is available for queries for 14 days, and data for advanced metrics and recommendations selections is available for queries for 15 months\.

**Topics**
+ [Disabling an Amazon S3 Storage Lens dashboard](storage_lens_console_disabling.md)
+ [Deleting an Amazon S3 Storage Lens dashboard](storage_lens_console_deleting.md)