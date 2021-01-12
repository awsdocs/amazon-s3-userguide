--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Registering delegated administrators for S3 Storage Lens<a name="storage_lens_console_organizations_registering_delegated_admins"></a>

After enabling trusted access, you can register delegate administrator access to accounts in your organization\. When an account is registered as a delegate administrator, the account receives authorization to access all read\-only AWS Organizations APIs\. This provides visibility to the members and structures of your organization so that they can create S3 Storage Lens dashboards on your behalf\.

**To register delegated administrators for S3 Storage Lens**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Storage Lens**, **Organization settings**\.

1. In the **delegated access** section, for **Accounts**, choose **Add account**\.

   The **Delegated admin access** page opens\. Here you can add an AWS account ID as a delegated administrator to create organization\-level dashboards for all accounts and storage in your organization\.