# AWS managed policies for Amazon S3<a name="security-iam-awsmanpol"></a>

To add permissions to users, groups, and roles, it is easier to use AWS managed policies than to write policies yourself\. It takes time and expertise to [create IAM customer managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create-console.html) that provide your team with only the permissions they need\. To get started quickly, you can use our AWS managed policies\. These policies cover common use cases and are available in your AWS account\. For more information about AWS managed policies, see [AWS managed policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html#aws-managed-policies) in the *IAM User Guide*\.

AWS services maintain and update AWS managed policies\. You can't change the permissions in AWS managed policies\. Services occasionally add additional permissions to an AWS managed policy to support new features\. This type of update affects all identities \(users, groups, and roles\) where the policy is attached\. Services are most likely to update an AWS managed policy when a new feature is launched or when new operations become available\. Services do not remove permissions from an AWS managed policy, so policy updates won't break your existing permissions\.

Additionally, AWS supports managed policies for job functions that span multiple services\. For example, the **ViewOnlyAccess** AWS managed policy provides read\-only access to many AWS services and resources\. When a service launches a new feature, AWS adds read\-only permissions for new operations and resources\. For a list and descriptions of job function policies, see [AWS managed policies for job functions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html) in the *IAM User Guide*\.

## AWS managed policy: AmazonS3FullAccess<a name="security-iam-awsmanpol-amazons3fullaccess"></a>

You can attach the `AmazonS3FullAccess` policy to your IAM identities\. This policy grants permissions that allow full access to Amazon S3\.

To view the permissions for this policy, see [AmazonS3FullAccess](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/AmazonS3FullAccess$jsonEditor) in the AWS Management Console\.

## AWS managed policy: AmazonS3ReadOnlyAccess<a name="security-iam-awsmanpol-amazons3readonlyaccess"></a>

You can attach the `AmazonS3ReadOnlyAccess` policy to your IAM identities\. This policy grants permissions that allow read\-only access to Amazon S3\.

To view the permissions for this policy, see [AmazonS3ReadOnlyAccess](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess$jsonEditor) in the AWS Management Console\.

## AWS managed policy: AmazonS3ObjectLambdaExecutionRolePolicy<a name="security-iam-awsmanpol-amazons3objectlambdaexecutionrolepolicy"></a>

Provides AWS Lambda functions the required permissions to send data to S3 Object Lambda when requests are made to an S3 Object Lambda access point\. Also grants Lambda permissions to write to Amazon CloudWatch logs\.

To view the permissions for this policy, see [AmazonS3ObjectLambdaExecutionRolePolicy](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/service-role/AmazonS3ObjectLambdaExecutionRolePolicy$jsonEditor) in the AWS Management Console\.

## Amazon S3 updates to AWS managed policies<a name="security-iam-awsmanpol-updates"></a>

View details about updates to AWS managed policies for Amazon S3 since this service began tracking these changes\.


| Change | Description | Date | 
| --- | --- | --- | 
|  Amazon S3 added S3 Object Lambda permissions to `AmazonS3FullAccess` and `AmazonS3ReadOnlyAccess`  |  Amazon S3 updated the `AmazonS3FullAccess` and `AmazonS3ReadOnlyAccess` policies to include permissions for S3 Object Lambda\.  | September 27, 2021 | 
|  Amazon S3 added `AmazonS3ObjectLambdaExecutionRolePolicy`  |  Amazon S3 added a new AWS\-managed policy called `AmazonS3ObjectLambdaExecutionRolePolicy` that provides Lambda functions permissions to interact with S3 Object Lambda and write to CloudWatch logs\.  | August 18, 2021 | 
|  Amazon S3 started tracking changes  |  Amazon S3 started tracking changes for its AWS managed policies\.  | August 18, 2021 | 