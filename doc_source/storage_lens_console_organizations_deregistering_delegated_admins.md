# Deregistering delegated administrators for S3 Storage Lens<a name="storage_lens_console_organizations_deregistering_delegated_admins"></a>

You can deregister delegate administrator access to accounts in your organization\. When an account is deregistered as a delegated administrator, the account loses authorization to access all read\-only AWS Organizations APIs that provide visibility to the members and structures of your organization\.

**Note**  
Deregistering a delegated administrator also automatically disables all organization\-level dashboards created by the delegated administrator\.
The delegate administrator accounts can still see the historic data for these disabled dashboards according to the respective period that data is available for queries\. 

**To deregister accounts for delegated administrator access**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Storage Lens**, **Organization settings**\.

1. In the **Accounts with delegated access** section, choose the account ID you want to deregister, and then choose **Remove**\.