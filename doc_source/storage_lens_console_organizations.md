# Working with AWS Organizations to create organization\-level dashboards<a name="storage_lens_console_organizations"></a>

S3 Storage Lens aggregates your metrics and displays the information in the **Account snapshot** section on the Amazon S3 console **Buckets** page\. S3 Storage Lens also provides an interactive dashboard that you can use to visualize insights and trends, flag outliers, and receive recommendations for optimizing storage costs and applying data\-protection best practices\. Your dashboard has drill\-down options to generate insights at the organization, account, bucket, object, or prefix level\. You can also send a once\-daily metrics export in CSV or Parquet format to an S3 bucket\.

The Amazon S3 Storage Lens default dashboard is **default\-account\-dashboard**\. This dashboard is preconfigured by Amazon S3 to help you visualize summarized insights and trends for your entire account's aggregated free and advanced metrics on the console\. You can't modify the default dashboard's configuration scope, but you can upgrade the metrics selection from the free metrics to the paid advanced metrics and recommendations, configure the optional metrics export, or even disable the default dashboard\. The default dashboard cannot be deleted\. 

You can also create additional S3 Storage Lens dashboards that are focused on specific AWS Regions, S3 buckets, or other AWS accounts in your organization\. 

The Amazon S3 dashboard provides a rich resource of information about its storage scope, representing more than 30 metrics that represent trends and information, including storage summary, cost efficiency, data protection, and activity\.

Amazon S3 Storage Lens can be used to collect storage metrics and usage data for all accounts that are part of your AWS Organizations hierarchy\. To do this, you must be using AWS Organizations, and you must enable S3 Storage Lens trusted access by using your AWS Organizations management account\. 

When trusted access is enabled, you can add delegate administrator access to accounts in your organization\. These accounts can then create organization\-wide dashboards and configurations for S3 Storage Lens\. For more information about enabling trusted access, see [Amazon S3 Lens and AWS Organizations](https://docs.aws.amazon.com/organizations/latest/userguide/services-that-can-integrate-s3lens.html) in the *AWS Organizations User Guide*\. 

 The following console controls are available only to the AWS Organizations management accounts\. 

**Topics**
+ [Enabling trusted access for S3 Storage Lens in your organization](storage_lens_console_organizations_enabling_trusted_access.md)
+ [Disabling S3 Storage Lens trusted access in your organization](storage_lens_console_organizations_disabling_trusted_access.md)
+ [Registering delegated administrators for S3 Storage Lens](storage_lens_console_organizations_registering_delegated_admins.md)
+ [Deregistering delegated administrators for S3 Storage Lens](storage_lens_console_organizations_deregistering_delegated_admins.md)