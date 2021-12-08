# Creating an access point<a name="create-access-points"></a>

An access point is associated with exactly one Amazon S3 bucket\. Before you begin, make sure that you have created a bucket that you want to use with this access point\. For more information about creating buckets, see [Creating, configuring, and working with Amazon S3 buckets](creating-buckets-s3.md)\. Amazon S3 access points support AWS Identity and Access Management \(IAM\) resource policies that allow you to control the use of the access point by resource, user, or other conditions\. For more information, see [Configuring IAM policies for using access points](access-points-policies.md)\.

Make sure to resolve security warnings, errors, general warnings, and suggestions from AWS Identity and Access Management Access Analyzer before you save your policy\. IAM Access Analyzer runs policy checks to validate your policy against IAM [policy grammar](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_grammar.html) and [best practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)\. These checks generate findings and provide actionable recommendations to help you author policies that are functional and conform to security best practices\. To learn more about validating policies using IAM Access Analyzer, see [IAM Access Analyzer policy validation](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-policy-validation.html) in the *IAM User Guide*\. To view a list of the warnings, errors, and suggestions that are returned by IAM Access Analyzer, see [IAM Access Analyzer policy check reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-reference-policy-checks.html)\.

By default, you can create up to 1,000 access points per Region for each of your AWS accounts\. If you need more than 1,000 access points for a single account in a single Region, you can request a service quota increase\. For more information about service quotas and requesting an increase, see [AWS Service Quotas](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html) in the *AWS General Reference*\.

The following examples demonstrate how to create an access point with the AWS CLI and the S3 console\. For more information about how to create access points using the REST API, see [CreateAccessPoint](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_CreateAccessPoint.html) in the *Amazon Simple Storage Service API Reference*\. 

## Using the S3 console<a name="access-points-create-ap"></a>

**To create an access point**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane on the left side of the console, choose **Access points**\.

1. On the access points page, choose **Create access point**\.

1. In the **Access point name** field, enter your desired name for the access point\. For more information about naming access points, see [Rules for naming Amazon S3 access points](creating-access-points.md#access-points-names)\.

1. In the **Bucket name** field, enter the name of a bucket in your account to which you want to attach the access point, for example *DOC\-EXAMPLE\-BUCKET1*\. Optionally, you can choose **Browse S3** to browse and search buckets in your account\. If you choose **Browse S3**, select the desired bucket and choose **Choose path** to populate the **Bucket name** field with that bucket's name\.

1. \(Optional\) Choose **View** to view the contents of the specified bucket in a new browser window\.

1. Select a **Network origin**\. If you choose **Virtual private cloud \(VPC\)**, enter the **VPC ID** that you want to use with the access point\.

   For more information about network origins for access points, see [Creating access points restricted to a virtual private cloud](access-points-vpc.md)\.

1. Under **Block Public Access settings for this Access Point**, select the block public access settings that you want to apply to the access point\. All block public access settings are enabled by default for new access points, and we recommend that you leave all settings enabled unless you know you have a specific need to disable any of them\. Amazon S3 currently doesn't support changing an access point's block public access settings after the access point has been created\.

   For more information about using Amazon S3 Block Public Access with access points, see [Managing public access to access points](access-points-bpa-settings.md)\.

1. \(Optional\) Under **Access Point policy \- *optional***, specify the access point policy\. Make sure to resolve security warnings, errors, general warnings, and suggestions before you save your policy\. For more information about specifying an access point policy, see [Access point policy examples](access-points-policies.md#access-points-policy-examples)\.

1. Choose **Create access point**\.

## Using the AWS CLI<a name="creating-access-point-cli"></a>

The following example creates an access point named `example-ap` for bucket `example-bucket` in account `123456789012`\. To create the access point, you send a request to Amazon S3, specifying the access point name, the name of the bucket that you want to associate the access point with, and the account ID for the AWS account that owns the bucket\. For information about naming rules, see [Rules for naming Amazon S3 access points](creating-access-points.md#access-points-names)\.

```
aws s3control create-access-point --name example-ap --account-id 123456789012 --bucket example-bucket
```