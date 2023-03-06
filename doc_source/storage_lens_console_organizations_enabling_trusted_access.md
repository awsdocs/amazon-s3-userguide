# Enabling trusted access for S3 Storage Lens in your organization<a name="storage_lens_console_organizations_enabling_trusted_access"></a>

Enabling trusted access allows Amazon S3 Storage Lens to access your AWS Organizations hierarchy, membership, and structure through AWS Organizations API operations\. S3 Storage Lens becomes a trusted service for your entire organization's structure\. It can create service\-linked roles in your organization's management or delegated administrator accounts whenever a dashboard configuration is created\. 

The service\-linked role grants S3 Storage Lens permissions to describe organizations, list accounts, verify a list of service access for the organizations, and get delegated administrators for the organization\. This allows S3 Storage Lens to collect cross\-account storage usage and activity metrics for dashboards within accounts in your organization\. 

For more information, see [Using service\-linked roles for Amazon S3 Storage Lens](using-service-linked-roles.md)\.

**Note**  
Trusted access can be enabled only by the *management account*\. 
 Only the management account and delegated administrators can create S3 Storage Lens dashboards or configurations for your organization\.

**To enable S3 Storage Lens to have trusted access**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Storage Lens**, **Organization settings**\.

1. In **Organizations access**, choose **Edit**\.

   The **Organization access** page opens\. Here you can **Enable trusted access** for S3 Storage Lens\. This allows you and any other account holders that you add as delegated administrators to create dashboards for all accounts and storage in your organization\.