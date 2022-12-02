# Using Amazon S3 Storage Lens with AWS Organizations<a name="storage_lens_with_organizations"></a>

Amazon S3 Storage Lens is a cloud\-storage analytics feature that you can use to gain organization\-wide visibility into object\-storage usage and activity\. You can use S3 Storage Lens metrics to generate summary insights, such as finding out how much storage you have across your entire organization or which are the fastest\-growing buckets and prefixes\. You can also use S3 Storage Lens metrics to identify cost\-optimization opportunities, implement data\-protection and security best practices, and improve the performance of application workloads\. For example, you can identify buckets that don't have S3 Lifecycle rules to expire incomplete multipart uploads that are more than 7 days old\. You can also identify buckets that aren't following data\-protection best practices, such as using S3 Replication or S3 Versioning\. S3 Storage Lens also analyzes metrics to deliver contextual recommendations that you can use to optimize storage costs and apply best practices for protecting your data\. 

You can use Amazon S3 Storage Lens to collect storage metrics and usage data for all AWS accounts that are part of your AWS Organizations hierarchy\. To do this, you must be using AWS Organizations, and you must enable S3 Storage Lens trusted access by using your AWS Organizations management account\. 

After enabling trusted access, you can add delegated administrator access to accounts in your organization\. These accounts can then create S3 Storage Lens configurations and dashboards that collect organization\-wide storage metrics and user data\.

For more information about enabling trusted access, see [Amazon S3 Storage Lens and AWS Organizations](https://docs.aws.amazon.com/organizations/latest/userguide/services-that-can-integrate-s3lens.html) in the *AWS Organizations User Guide*\.

**Topics**
+ [Enabling trusted access for S3 Storage Lens](#storage_lens_with_organizations_enabling_trusted_access)
+ [Disabling trusted access for S3 Storage Lens](#storage_lens_with_organizations_disabling_trusted_access)
+ [Registering a delegated administrator for S3 Storage Lens](#storage_lens_with_organizations_registering_delegated_admins)
+ [Deregistering a delegated administrator for S3 Storage Lens](#storage_lens_with_organizations_deregistering_delegated_admins)

## Enabling trusted access for S3 Storage Lens<a name="storage_lens_with_organizations_enabling_trusted_access"></a>

By enabling trusted access, you allow Amazon S3 Storage Lens to have access to your AWS Organizations hierarchy, membership, and structure through AWS Organizations API operations\. S3 Storage Lens then becomes a trusted service for your entire organization's structure\.

Whenever a dashboard configuration is created, S3 Storage Lens creates service\-linked roles in your organization's management or delegated administrator accounts\. The service\-linked role grants S3 Storage Lens permissions to describe organizations, list accounts, verify a list of AWS service access for the organizations, and get delegated administrators for the organization\. S3 Storage Lens can then ensure that it has access to collect the cross\-account metrics for the accounts in your organizations\. For more information, see [ Using service\-linked roles for Amazon S3 Storage Lens](https://docs.aws.amazon.com/AmazonS3/latest/userguide/using-service-linked-roles.html)\. 

After enabling trusted access, you can assign delegated administrator access to accounts in your organization\. When an account is marked as a delegated administrator for a service, the account receives authorization to access all read\-only organization API operations\. This access provides the delegated administrator visibility to the members and structures of your organization so that they too can create S3 Storage Lens dashboards\.

**Note**  
Only the management account can enable trusted access for Amazon S3 Storage Lens\.

## Disabling trusted access for S3 Storage Lens<a name="storage_lens_with_organizations_disabling_trusted_access"></a>

By disabling trusted access, you limit S3 Storage Lens to working only on an account level\. In addition, each account holder can see only the S3 Storage Lens information for the scope of their account, and not their entire organization\. Any dashboards that require trusted access are no longer updated, but will retain their historic data for the period that [data is available for queries](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage_lens_basics_metrics_recommendations.html#storage_lens_basics_data_queries)\. 



**Note**  
Disabling trusted access for S3 Storage Lens also automatically stops all organization\-level dashboards from collecting and aggregating storage metrics\. 
Your management and delegated administrator accounts will still be able to see the historic data for your existing organization\-level dashboards during the period that data is available for queries\.

## Registering a delegated administrator for S3 Storage Lens<a name="storage_lens_with_organizations_registering_delegated_admins"></a>

You can create organization\-level dashboards by using your organization’s management account or delegated administrator accounts\. Delegated administrator accounts allow other accounts besides your management account to create organization\-level dashboards\. Only the management account of an organization can register and deregister other accounts as delegated administrators for the organization\.

To register a delegated administrator by using the Amazon S3 console, see [Registering delegated administrators for S3 Storage Lens](storage_lens_console_organizations_registering_delegated_admins.md)\.

You can also register a delegated administrator by using the AWS Organizations REST API, AWS CLI, or SDKs from the management account\. For more information, see [https://docs.aws.amazon.com/organizations/latest/APIReference/API_RegisterDelegatedAdministrator.html](https://docs.aws.amazon.com/organizations/latest/APIReference/API_RegisterDelegatedAdministrator.html) in the *AWS Organizations API Reference*\.

**Note**  
Before you can designate a delegated administrator by using the AWS Organizations REST API, AWS CLI, or SDKs, you must call the [https://docs.aws.amazon.com/servicecatalog/latest/dg/API_EnableAWSOrganizationsAccess.html](https://docs.aws.amazon.com/servicecatalog/latest/dg/API_EnableAWSOrganizationsAccess.html) operation\.

## Deregistering a delegated administrator for S3 Storage Lens<a name="storage_lens_with_organizations_deregistering_delegated_admins"></a>

You can also deregister a delegated administrator account\. Delegated administrator accounts allow other accounts besides your management account to create organization\-level dashboards\. Only the management account of an organization can deregister accounts as delegated administrators for the organization\.

To deregister a delegated administrator by using the S3 console, see [Deregistering delegated administrators for S3 Storage Lens](storage_lens_console_organizations_deregistering_delegated_admins.md)\.

You can also deregister a delegated administrator by using the AWS Organizations REST API, AWS CLI, or SDKs from the management account\. For more information, see [https://docs.aws.amazon.com/organizations/latest/APIReference/API_DeregisterDelegatedAdministrator.html](https://docs.aws.amazon.com/organizations/latest/APIReference/API_DeregisterDelegatedAdministrator.html) in the *AWS Organizations API Reference*\.

**Note**  
Deregistering a delegated administrator also automatically stops all organization\-level dashboards created by that delegated administrator from aggregating new storage metrics\.
The deregistered delegated administrator will still be able to see the historic data for the dashboards that they created while data is available for queries\.