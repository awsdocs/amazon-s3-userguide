--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Prerequisite: Setting up Amazon S3<a name="setting-up-s3"></a>

When you sign up for AWS, your AWS account is automatically signed up for all services in AWS, including Amazon S3\. You are charged only for the services that you use\.

With Amazon S3, you pay only for what you use\. For more information about Amazon S3 features and pricing, see [Amazon S3](http://aws.amazon.com/s3)\. If you are a new Amazon S3 customer, you can get started with Amazon S3 for free\. For more information, see [AWS Free Tier](http://aws.amazon.com/free)\.

To set up Amazon S3, use the steps in the following sections\.

When you sign up for AWS and set up Amazon S3, you can optionally change the display language in the AWS Management Console\. For more information, see [Changing the language of the AWS Management Console?](change-ui-language.md)\.

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

AWS sends you a confirmation email after the sign\-up process is complete\. At any time, you can view your current account activity and manage your account by going to [https://aws\.amazon\.com/](https://aws.amazon.com/) and choosing **My Account**\.

## Create an IAM user<a name="create-an-iam-user-gsg"></a>

When you first create an Amazon Web Services \(AWS\) account, you begin with a single sign\-in identity\. That identity has complete access to all AWS services and resources in the account\. This identity is called the AWS account *root user*\. When you sign in, enter the email address and password that you used to create the account\.

**Important**  
We strongly recommend that you do not use the root user for your everyday tasks, even the administrative ones\. Instead, adhere to the [best practice of using the root user only to create your first IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html#create-iam-users)\. Then securely lock away the root user credentials and use them to perform only a few account and service management tasks\. To view the tasks that require you to sign in as the root user, see [AWS Tasks That Require Root User](https://docs.aws.amazon.com/general/latest/gr/aws_tasks-that-require-root.html)\.

If you signed up for AWS but have not created an IAM user for yourself, follow these steps\.

**To create an administrator user for yourself and add the user to an administrators group \(console\)**

1. Sign in to the [IAM console](https://console.aws.amazon.com/iam/) as the account owner by choosing **Root user** and entering your AWS account email address\. On the next page, enter your password\.
**Note**  
We strongly recommend that you adhere to the best practice of using the **Administrator** IAM user below and securely lock away the root user credentials\. Sign in as the root user only to perform a few [account and service management tasks](https://docs.aws.amazon.com/general/latest/gr/aws_tasks-that-require-root.html)\.

1. In the navigation pane, choose **Users** and then choose **Add user**\.

1. For **User name**, enter **Administrator**\.

1. Select the check box next to **AWS Management Console access**\. Then select **Custom password**, and then enter your new password in the text box\.

1. \(Optional\) By default, AWS requires the new user to create a new password when first signing in\. You can clear the check box next to **User must create a new password at next sign\-in** to allow the new user to reset their password after they sign in\.

1. Choose **Next: Permissions**\.

1. Under **Set permissions**, choose **Add user to group**\.

1. Choose **Create group**\.

1. In the **Create group** dialog box, for **Group name** enter **Administrators**\.

1. Choose **Filter policies**, and then select **AWS managed \-job function** to filter the table contents\.

1. In the policy list, select the check box for **AdministratorAccess**\. Then choose **Create group**\.
**Note**  
You must activate IAM user and role access to Billing before you can use the `AdministratorAccess` permissions to access the AWS Billing and Cost Management console\. To do this, follow the instructions in [step 1 of the tutorial about delegating access to the billing console](https://docs.aws.amazon.com/IAM/latest/UserGuide/tutorial_billing.html)\.

1. Back in the list of groups, select the check box for your new group\. Choose **Refresh** if necessary to see the group in the list\.

1. Choose **Next: Tags**\.

1. \(Optional\) Add metadata to the user by attaching tags as key\-value pairs\. For more information about using tags in IAM, see [Tagging IAM entities](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_tags.html) in the *IAM User Guide*\.

1. Choose **Next: Review** to see the list of group memberships to be added to the new user\. When you are ready to proceed, choose **Create user**\.

You can use this same process to create more groups and users and to give your users access to your AWS account resources\. To learn about using policies that restrict user permissions to specific AWS resources, see [Access management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) and [Example policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_examples.html)\.

## Sign in as an IAM user<a name="signing-in-iam-user-gsg"></a>

After you create an IAM user, you can sign in to AWS with your IAM user name and password\.

Before you sign in as an IAM user, you can verify the sign\-in link for IAM users in the IAM console\. On the IAM Dashboard, under **IAM users sign\-in link**, you can see the sign\-in link for your AWS account\. The URL for your sign\-in link contains your AWS account ID without dashes \(‐\)\. 

If you don't want the URL for your sign\-in link to contain your AWS account ID, you can create an account alias\. For more information, see [Creating, deleting, and listing an AWS account alias](https://docs.aws.amazon.com/IAM/latest/UserGuide/console_account-alias.html#CreateAccountAlias) in the *IAM User Guide*\.

**To sign in as an AWS user**

1. Sign out of the AWS Management Console\.

1. Enter your sign\-in link\.

   Your sign\-in link includes your AWS account ID \(without dashes\) or your AWS account alias:

   ```
   https://aws_account_id_or_alias.signin.aws.amazon.com/console
   ```

1. Enter the IAM user name and password that you just created\. 

   When you're signed in, the navigation bar displays "*your\_user\_name* @ *your\_aws\_account\_id*"\. 