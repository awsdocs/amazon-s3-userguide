--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Disabling S3 Storage Lens trusted access in your organization<a name="storage_lens_console_organizations_disabling_trusted_access"></a>

 Disabling trusted access will limit S3 Storage Lens to only work on an account level\. Each account holder will only be able to see the benefits of S3 Storage Lens limited to the scope of their account, and not their organization\. Any dashboards requiring trusted access will no longer be updated, but they will retain their historic data per their respective [retention periods](https://docs.aws.amazon.com/AmazonS3/latest/dev/storage_lens_basics_metrics_recommendations.html#storage_lens_basics_retention_period)\. 

Removing an account as a delegated administrator limits their S3 Storage Lens dashboard metrics access to only work on an account level\. Any organizational dashboards that they created will no longer be updated, but they will retain their historic data per their respective [retention periods](https://docs.aws.amazon.com/AmazonS3/latest/dev/storage_lens_basics_metrics_recommendations.html#storage_lens_basics_retention_period)\. 

**Note**  
 Disabling trusted access also automatically disables all organization\-level dashboards because S3 Storage Lens will no longer have trusted access to the organization accounts to collect and aggregate storage metrics\.
The management and delegate administrator accounts can still see the historic data for these disabled dashboards according to their respective retention periods\. 

**To disable trusted access for S3 Storage Lens**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Storage Lens**, **Organization settings**\.

1. In **Organizations access**, choose **Edit**\.

   The **Organization access** page opens\. Here you can **Disable trusted access** for S3 Storage Lens\.