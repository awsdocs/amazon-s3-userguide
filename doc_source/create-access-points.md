# Creating an access point<a name="create-access-points"></a>

An access point is associated with exactly one Amazon S3 bucket\. If you want to use a bucket in your AWS account, you must first create a bucket\. For more information about creating buckets, see [Creating, configuring, and working with Amazon S3 buckets](creating-buckets-s3.md)\.

You can also create a cross\-account access point that's associated with a bucket in another AWS account, as long as you know the bucket name and the bucket owner's account ID\. However, creating cross\-account access points doesn't grant you access to data in the bucket until you are granted permissions from the bucket owner\. The bucket owner must grant the access point owner's account \(your account\) access to the bucket through the bucket policy\. For more information, see [Granting permissions for cross\-account access points](access-points-policies.md#access-points-cross-account)\.

By default, you can create up to 10,000 access points per Region for each of your AWS accounts\. If you need more than 10,000 access points for a single account in a single Region, you can request a service quota increase\. For more information about service quotas and requesting an increase, see [AWS Service Quotas](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html) in the *AWS General Reference*\.

The following examples demonstrate how to create an access point with the AWS CLI and the S3 console\. For more information about how to create access points by using the REST API, see [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_CreateAccessPoint.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_CreateAccessPoint.html) in the *Amazon Simple Storage Service API Reference*\. 

## Using the S3 console<a name="access-points-create-ap"></a>

**To create an access point**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Access Points**\.

1. On the **Access Points** page, choose **Create access point**\.

1. In the **Access point name** field, enter the name for the access point\. For more information about naming access points, see [Rules for naming Amazon S3 access points](creating-access-points.md#access-points-names)\.

1. For **Bucket name**, specify the S3 bucket that you want to use with the access point\.

   To use a bucket in your account, choose **Choose a bucket in this account**, and enter or browse for the bucket name\. 

   To use a bucket in a different AWS account, choose **Specify a bucket in another account**, and enter the AWS account ID and name of the bucket\. 
**Note**  
If you're using a bucket in a different AWS account, the bucket owner must update the bucket policy to authorize requests from the access point\. For an example bucket policy, see [Granting permissions for cross\-account access points](access-points-policies.md#access-points-cross-account)\.

1. Choose a **Network origin**\. If you choose **Virtual private cloud \(VPC\)**, enter the **VPC ID** that you want to use with the access point\.

   For more information about network origins for access points, see [Creating access points restricted to a virtual private cloud](access-points-vpc.md)\.

1. Under **Block Public Access settings for this Access Point**, select the block public access settings that you want to apply to the access point\. All block public access settings are enabled by default for new access points\. We recommend that you keep all settings enabled unless you know that you have a specific need to disable any of them\. 
**Note**  
After you create an access point, you can't change its block public access settings\.

   For more information about using Amazon S3 Block Public Access with access points, see [Managing public access to access points](access-points-bpa-settings.md)\.

1. \(Optional\) Under **Access Point policy \- *optional***, specify the access point policy\. Before you save your policy, make sure to resolve any security warnings, errors, general warnings, and suggestions\. For more information about specifying an access point policy, see [Access point policy examples](access-points-policies.md#access-points-policy-examples)\.

1. Choose **Create access point**\.

## Using the AWS CLI<a name="creating-access-point-cli"></a>

The following example command creates an access point named *`example-ap`* for the bucket *`DOC-EXAMPLE-BUCKET`* in the account *`111122223333`*\. To create the access point, you send a request to Amazon S3 that specifies the following:
+ The access point name\. For information about naming rules, see [Rules for naming Amazon S3 access points](creating-access-points.md#access-points-names)\.
+ The name of the bucket that you want to associate the access point with\.
+ The account ID for the AWS account that owns the bucket\.

```
aws s3control create-access-point --name example-ap --account-id 111122223333 --bucket DOC-EXAMPLE-BUCKET
```

When you're creating an access point by using a bucket in a different AWS account, include the `--bucket-account-id` parameter\. The following example command creates an access point in the AWS account *`111122223333`*, using the bucket *`DOC-EXAMPLE-BUCKET2`*, which is in the AWS account *`444455556666`*\.

```
aws s3control create-access-point --name example-ap --account-id 111122223333 --bucket DOC-EXAMPLE-BUCKET --bucket-account-id 444455556666
```