# Prerequisite: Setting up Amazon S3<a name="setting-up-s3"></a>

When you sign up for AWS, your AWS account is automatically signed up for all services in AWS, including Amazon S3\. You are charged only for the services that you use\.

With Amazon S3, you pay only for what you use\. For more information about Amazon S3 features and pricing, see [Amazon S3](http://aws.amazon.com/s3)\. If you are a new Amazon S3 customer, you can get started with Amazon S3 for free\. For more information, see [AWS Free Tier](http://aws.amazon.com/free)\.

To set up Amazon S3, use the steps in the following sections\.

When you sign up for AWS and set up Amazon S3, you can optionally change the display language in the AWS Management Console\. For more information, see [Changing the language of the AWS Management Console](https://docs.aws.amazon.com/awsconsolehelpdocs/latest/gsg/getting-started.html#change-language) in the *AWS Management Console Getting Started Guide*\.

**Topics**
+ [Sign up for AWS](#sign-up-for-aws-gsg)
+ [Create an IAM user](#create-an-iam-user-gsg)
+ [Sign in as an IAM user](#signing-in-iam-user-gsg)

## Sign up for AWS<a name="sign-up-for-aws-gsg"></a>

If you do not have an AWS account, complete the following steps to create one\.

**To sign up for an AWS account**

1. Open [https://portal\.aws\.amazon\.com/billing/signup](https://portal.aws.amazon.com/billing/signup)\.

1. Follow the online instructions\.

   Part of the sign\-up procedure involves receiving a phone call and entering a verification code on the phone keypad\.

   When you sign up for an AWS account, an *AWS account root user* is created\. The root user has access to all AWS services and resources in the account\. As a security best practice, [assign administrative access to an administrative user](https://docs.aws.amazon.com/singlesignon/latest/userguide/getting-started.html), and use only the root user to perform [tasks that require root user access](https://docs.aws.amazon.com/general/latest/gr/root-vs-iam.html#aws_tasks-that-require-root)\.

AWS sends you a confirmation email after the sign\-up process is complete\. At any time, you can view your current account activity and manage your account by going to [https://aws\.amazon\.com/](https://aws.amazon.com/) and choosing **My Account**\.

## Create an IAM user<a name="create-an-iam-user-gsg"></a>

When you first create an AWS account, you begin with one sign\-in identity that has complete access to all AWS services and resources in the account\. This identity is called the AWS account *root user* and is accessed by signing in with the email address and password that you used to create the account\.

**Important**  
We strongly recommend that you do not use the root user for your everyday tasks\. Safeguard your root user credentials and use them to perform the tasks that only the root user can perform\. For the complete list of tasks that require you to sign in as the root user, see [Tasks that require root user credentials](https://docs.aws.amazon.com/general/latest/gr/root-vs-iam.html#aws_tasks-that-require-root) in the *AWS General Reference*\.

If you signed up for AWS but have not created an IAM user for yourself, follow these steps\.

To create an administrator user, choose one of the following options\.


****  

| Choose one way to manage your administrator | To | By | You can also | 
| --- | --- | --- | --- | 
| In IAM Identity Center \(Recommended\) | Use short\-term credentials to access AWS\.This aligns with the security best practices\. For information about best practices, see [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#bp-users-federation-idp) in the *IAM User Guide*\. | Following the instructions in [Getting started](https://docs.aws.amazon.com/singlesignon/latest/userguide/getting-started.html) in the AWS IAM Identity Center \(successor to AWS Single Sign\-On\) User Guide\. | Configure programmatic access by [Configuring the AWS CLI to use AWS IAM Identity Center \(successor to AWS Single Sign\-On\)](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-sso.html) in the AWS Command Line Interface User Guide\. | 
| In IAM \(Not recommended\) | Use long\-term credentials to access AWS\. | Following the instructions in [Creating your first IAM admin user and user group](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html) in the IAM User Guide\. | Configure programmatic access by [Managing access keys for IAM users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) in the IAM User Guide\. | 

## Sign in as an IAM user<a name="signing-in-iam-user-gsg"></a>

After you create an IAM user, you can sign in to AWS with your IAM user name and password\.

Before you sign in as an IAM user, you can verify the sign\-in link for IAM users in the IAM console\. On the IAM Dashboard, under **IAM users sign\-in link**, you can see the sign\-in link for your AWS account\. The URL for your sign\-in link contains your AWS account ID without dashes \(‐\)\. 

If you don't want the URL for your sign\-in link to contain your AWS account ID, you can create an account alias\. For more information, see [Creating, deleting, and listing an AWS account alias](https://docs.aws.amazon.com/IAM/latest/UserGuide/console_account-alias.html#CreateAccountAlias) in the *IAM User Guide*\.

**To sign in as an AWS user**

1. Sign out of the AWS Management Console\.

1. Enter your sign\-in link\.

   Your sign\-in link includes your AWS account ID \(without dashes\) or your AWS account alias:

   ```
   https://AccountIDorAlias.signin.aws.amazon.com/console
   ```

1. Enter the IAM user name and password that you just created\. 

   When you're signed in, the navigation bar displays "*your\_user\_name* @ *your\_aws\_account\_id*"\. 