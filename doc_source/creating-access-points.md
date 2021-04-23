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

To create an access point, see the topics below\.

**Topics**
+ [Rules for naming Amazon S3 access points](#access-points-names)
+ [Creating an access point](create-access-points.md)
+ [Creating access points restricted to a virtual private cloud](access-points-vpc.md)
+ [Managing public access to access points](access-points-bpa-settings.md)