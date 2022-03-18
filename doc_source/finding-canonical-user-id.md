# Finding the canonical user ID for your AWS account<a name="finding-canonical-user-id"></a>

The canonical user ID is an alpha\-numeric identifier, such as `79a59df900b949e55d96a1e698fbacedfd6e09d98eacf8f8d5218e7cd47ef2be`, that is an obfuscated form of the AWS account ID\. You can use this ID to identify an AWS account when granting cross\-account access to buckets and objects using Amazon S3\. You can retrieve the canonical user ID for your AWS account as either the root user or an IAM user\. 

You can find the canonical user ID for your AWS account using the AWS Management Console or the AWS CLI\. The canonical user ID for an AWS account is specific to that account\. You can retrieve the canonical user ID for your account as the root user, a federated user, or an IAM user\. 

**Prerequisites**  
If you are a federated user or are accessing the information programmatically, such as through the AWS CLI, you must have permission to list and view an Amazon S3 bucket\.

## Using the S3 console \(root user or an IAM user\)<a name="canonical-id-root-iam-user"></a>

Follow these steps to find the canonical user ID for your AWS account when you are signed into the console as the root user or an IAM user\. For more information about the root user and IAM users, see [Overview of AWS identity management: Users](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_identity-management.html) in the *IAM User Guide*\.

1. Sign in to the console as the root user or an IAM user\.

   For more information, see [Signing in to the AWS Management Console](https://docs.aws.amazon.com/IAM/latest/UserGuide/console.html) in the *IAM User Guide*\.

1. In the navigation bar on the upper right, choose your account name or number, and then choose **Security credentials**\.

1. Find the canonical ID for the account:
   + If you are the root user, expand **Account identifiers** and find **Canonical User ID**\.
   + If you are an IAM user, under **Account details**, find **Account canonical user ID**\.

## Using the S3 console \(federated user\)<a name="canonical-id-federated-user"></a>

Follow these steps to find the canonical user ID for your account when you are signed in to the AWS Management Console as a federated user\. For more information about federated users, see [Federating existing users](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction_identity-management.html##intro-identity-federation) in the *IAM User Guide*\.

1. Sign in to the console as a federated user\.

   For more information, see [Signing in to the AWS Management Console](https://docs.aws.amazon.com/IAM/latest/UserGuide/console.html) in the *IAM User Guide*\.

1. In the Amazon S3 console, choose a bucket name to view the bucket details\.

1. Choose **Permissions**, and then scroll down to the **Access Control List** section\.

   At the top of the page, under **Access for bucket owner**, the canonical user ID for the AWS account appears\.

## Using the AWS CLI<a name="canonical-user-id-cli"></a>

Use the [list\-buckets](https://docs.aws.amazon.com/cli/latest/reference/s3api/list-buckets.html) command as follows to find the canonical user ID using the AWS CLI\.

```
aws s3api list-buckets --query Owner.ID --output text
```
