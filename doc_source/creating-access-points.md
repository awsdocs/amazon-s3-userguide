--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Creating access points<a name="creating-access-points"></a>

Amazon S3 provides functionality for creating and managing access points\. You can create S3 access points using the AWS Management Console, AWS Command Line Interface \(AWS CLI\), AWS SDKs, or Amazon S3 REST API\. 

By default, you can create up to 1,000 access points per Region for each of your AWS accounts\. If you need more than 1,000 access points for a single account in a single Region, you can request a service quota increase\. For more information about service quotas and requesting an increase, see [AWS Service Quotas](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html) in the *AWS General Reference*\.

**Note**  
Because you might want to publicize your access point name in order to allow users to use the access point, we recommend that you avoid including sensitive information in the access point name\.

## Rules for naming Amazon S3 access points<a name="access-points-names"></a>

Access point names must meet the following conditions:
+ Must be unique within a single AWS account and Region
+ Must comply with DNS naming restrictions
+ Must begin with a number or lowercase letter
+ Must be between 3 and 50 characters long
+ Can't begin or end with a dash
+ Can't contain underscores, uppercase letters, or periods

## Create access points<a name="create-access-points"></a>

An access point is associated with exactly one Amazon S3 bucket\. Before you begin, make sure that you have created a bucket that you want to use with this access point\. For more information about creating buckets, see [Creating, configuring, and working with Amazon S3 buckets](creating-buckets-s3.md)\. Amazon S3 access points support AWS Identity and Access Management \(IAM\) resource policies that allow you to control the use of the access point by resource, user, or other conditions\. For more information, see [Configuring IAM policies for using access points](access-points-policies.md)\.

The following examples demonstrate how to create an access point with the AWS CLI and the S3 console\. For more information about how to create access points using the REST API, see [CreateAccessPoint](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_CreateAccessPoint.html) in the *Amazon Simple Storage Service API Reference*\.

### Using the S3 console<a name="access-points-create-ap"></a>

**To create an access point**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane on the left side of the console, choose **Access points**\.

1. On the access points page, choose **Create access point**\.

1. In the **Access point name** field, enter your desired name for the access point\. For more information about naming access points, see [Rules for naming Amazon S3 access points](#access-points-names)\.

1. In the **Bucket name** field, enter the name of a bucket in your account to which you want to attach the access point, for example *DOC\-EXAMPLE\-BUCKET1*\. Optionally, you can choose **Browse S3** to browse and search buckets in your account\. If you choose **Browse S3**, select the desired bucket and choose **Choose path** to populate the **Bucket name** field with that bucket's name\.

1. \(Optional\) Choose **View** to view the contents of the specified bucket in a new browser window\.

1. Select a **Network origin**\. If you choose **Virtual private cloud \(VPC\)**, enter the **VPC ID** that you want to use with the access point\.

   For more information about network origins for access points, see [Using the AWS CLI to create access points restricted to a virtual private cloud](#access-points-vpc)\.

1. Under **Access point settings for Block Public Access**, select the block public access settings that you want to apply to the access point\. All block public access settings are enabled by default for new access points, and we recommend that you leave all settings enabled unless you know you have a specific need to disable any of them\. Amazon S3 currently doesn't support changing an access point's block public access settings after the access point has been created\.

   For more information about using Amazon S3 Block Public Access with access points, see [Using the AWS CLI to specify block public access settings when you create an access point](#access-points-bpa-settings)\.

1. \(Optional\) Under **Access point policy \- *optional***, specify the access point policy\. For more information about specifying an access point policy, see [Access point policy examples](access-points-policies.md#access-points-policy-examples)\.

1. Choose **Create access point**\.

### Using the AWS CLI<a name="creating-access-point-cli"></a>

The following example creates an access point named `example-ap` for bucket `example-bucket` in account `123456789012`\. To create the access point, you send a request to Amazon S3, specifying the access point name, the name of the bucket that you want to associate the access point with, and the account ID for the AWS account that owns the bucket\. For information about naming rules, see [Rules for naming Amazon S3 access points](#access-points-names)\.

```
aws s3control create-access-point --name example-ap --account-id 123456789012 --bucket example-bucket
```

### Using the AWS CLI to create access points restricted to a virtual private cloud<a name="access-points-vpc"></a>

When you create an access point, you can choose to make the access point accessible from the internet, or you can specify that all requests made through that access point must originate from a specific virtual private cloud \(VPC\)\. An access point that's accessible from the internet is said to have a network origin of `Internet`\. It can be used from anywhere on the internet, subject to any other access restrictions in place for the access point, underlying bucket, and related resources, such as the requested objects\. An access point that's only accessible from a specified VPC has a network origin of `VPC`, and Amazon S3 rejects any request made to the access point that doesn't originate from that VPC\.

**Important**  
You can only specify an access point's network origin when you create the access point\. After you create the access point, you can't change its network origin\.

To restrict an access point to VPC\-only access, you include the `VpcConfiguration` parameter with the request to create the access point\. In the `VpcConfiguration` parameter, you specify the VPC ID that you want to be able to use the access point\. Amazon S3 then rejects requests made through the access point unless they originate from that VPC\.

You can retrieve an access point's network origin using the AWS CLI, AWS SDKs, or REST APIs\. If an access point has a VPC configuration specified, its network origin is `VPC`\. Otherwise, the access point's network origin is `Internet`\.

**Example**  
***Example: Create an Access Point Restricted to VPC Access***  
The following example creates an access point named `example-vpc-ap` for bucket `example-bucket` in account `123456789012` that allows access only from VPC `vpc-1a2b3c`\. The example then verifies that the new access point has a network origin of `VPC`\.  

```
aws s3control create-access-point --name example-vpc-ap --account-id 123456789012 --bucket example-bucket --vpc-configuration VpcId=vpc-1a2b3c
```

```
aws s3control get-access-point --name example-vpc-ap --account-id 123456789012

{
    "Name": "example-vpc-ap",
    "Bucket": "example-bucket",
    "NetworkOrigin": "VPC",
    "VpcConfiguration": {
        "VpcId": "vpc-1a2b3c"
    },
    "PublicAccessBlockConfiguration": {
        "BlockPublicAcls": true,
        "IgnorePublicAcls": true,
        "BlockPublicPolicy": true,
        "RestrictPublicBuckets": true
    },
    "CreationDate": "2019-11-27T00:00:00Z"
}
```

To use an access point with a VPC, you must modify the access policy for your VPC endpoint\. VPC endpoints allow traffic to flow from your VPC to Amazon S3\. They have access\-control policies that control how resources within the VPC are allowed to interact with S3\. Requests from your VPC to S3 only succeed through an access point if the VPC endpoint policy grants access to both the access point and the underlying bucket\.

The following example policy statement configures a VPC endpoint to allow calls to `GetObject` for a bucket named `awsexamplebucket1` and an access point named `example-vpc-ap`\.

```
{
    "Version": "2012-10-17",
    "Statement": [
    {
        "Principal": "*",
        "Action": [
            "s3:GetObject"
        ],
        "Effect": "Allow",
        "Resource": [
            "arn:aws:s3:::awsexamplebucket1/*",
            "arn:aws:s3:us-west-2:123456789012:accesspoint/example-vpc-ap/object/*"
        ]
    }]
}
```

**Note**  
The `"Resource"` declaration in this example uses an Amazon Resource Name \(ARN\) to specify the access point\. For more information about access point ARNs, see [Managing and using access points](using-access-points.md)\. 

For more information about VPC endpoint policies, see [Using Endpoint Policies for Amazon S3](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-endpoints-s3.html#vpc-endpoints-policies-s3) in the *virtual private cloud \(VPC\) User Guide*\.

### Using the AWS CLI to specify block public access settings when you create an access point<a name="access-points-bpa-settings"></a>

Amazon S3 access points support independent *block public access* settings for each access point\. When you create an access point, you can specify block public access settings that apply to that access point\. For any request made through an access point, Amazon S3 evaluates the block public access settings for that access point, the underlying bucket, and the bucket owner's account\. If any of these settings indicate that the request should be blocked, Amazon S3 rejects the request\.

For more information about the S3 Block Public Access feature, see [Blocking public access to your Amazon S3 storage](access-control-block-public-access.md)\.

**Important**  
All block public access settings are enabled by default for access points\. You must explicitly disable any settings that you don't want to apply to an access point\.
Amazon S3 currently doesn't support changing an access point's block public access settings after the access point has been created\.

**Example**  
***Example: Create an Access Point with Custom Block Public Access Settings***  
This example creates an access point named `example-ap` for bucket `example-bucket` in account `123456789012` with non\-default Block Public Access settings\. The example then retrieves the new access point's configuration to verify its Block Public Access settings\.  

```
aws s3control create-access-point --name example-ap --account-id 123456789012 --bucket example-bucket --public-access-block-configuration BlockPublicAcls=false,IgnorePublicAcls=false,BlockPublicPolicy=true,RestrictPublicBuckets=true
```

```
aws s3control get-access-point --name example-ap --account-id 123456789012

{
    "Name": "example-ap",
    "Bucket": "example-bucket",
    "NetworkOrigin": "Internet",
    "PublicAccessBlockConfiguration": {
        "BlockPublicAcls": false,
        "IgnorePublicAcls": false,
        "BlockPublicPolicy": true,
        "RestrictPublicBuckets": true
    },
    "CreationDate": "2019-11-27T00:00:00Z"
}
```