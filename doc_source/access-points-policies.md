# Configuring IAM policies for using access points<a name="access-points-policies"></a>

Amazon S3 access points support AWS Identity and Access Management \(IAM\) resource policies that allow you to control the use of the access point by resource, user, or other conditions\. For an application or user to be able to access objects through an access point, both the access point and the underlying bucket must permit the request\.

**Important**  
Adding an S3 access point to a bucket doesn't change the bucket's behavior when the bucket is accessed directly through the bucket's name or Amazon Resource Name \(ARN\)\. All existing operations against the bucket will continue to work as before\. Restrictions that you include in an access point policy apply only to requests made through that access point\. 

When you're using IAM resource policies, make sure to resolve security warnings, errors, general warnings, and suggestions from AWS Identity and Access Management Access Analyzer before you save your policy\. IAM Access Analyzer runs policy checks to validate your policy against IAM [policy grammar](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_grammar.html) and [best practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)\. These checks generate findings and provide recommendations to help you author policies that are functional and conform to security best practices\. 

To learn more about validating policies by using IAM Access Analyzer, see [IAM Access Analyzer policy validation](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-policy-validation.html) in the *IAM User Guide*\. To view a list of the warnings, errors, and suggestions that are returned by IAM Access Analyzer, see [IAM Access Analyzer policy check reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-reference-policy-checks.html)\.

## Access point policy examples<a name="access-points-policy-examples"></a>

The following examples demonstrate how to create IAM policies to control requests made through an access point\.

**Note**  
Permissions granted in an access point policy are effective only if the underlying bucket also allows the same access\. You can accomplish this in two ways:  
**\(Recommended\)** Delegate access control from the bucket to the access point, as described in [Delegating access control to access points](#access-points-delegating-control)\.
Add the same permissions contained in the access point policy to the underlying bucket's policy\. The Example 1 access point policy example demonstrates how to modify the underlying bucket policy to allow the necessary access\.

**Example 1 – Access point policy grant**  
The following access point policy grants IAM user `Jane` in account `123456789012` permissions to `GET` and `PUT` objects with the prefix `Jane/` through the access point *`my-access-point`* in account *`123456789012`*\.  

```
{
    "Version":"2012-10-17",
    "Statement": [
    {
        "Effect": "Allow",
        "Principal": {
            "AWS": "arn:aws:iam::123456789012:user/Jane"
        },
        "Action": ["s3:GetObject", "s3:PutObject"],
        "Resource": "arn:aws:s3:us-west-2:123456789012:accesspoint/my-access-point/object/Jane/*"
    }]
}
```

**Note**  
For the access point policy to effectively grant access to *`Jane`*, the underlying bucket must also allow the same access to *`Jane`*\. You can delegate access control from the bucket to the access point as described in [Delegating access control to access points](#access-points-delegating-control)\. Or, you can add the following policy to the underlying bucket to grant the necessary permissions to Jane\. Note that the `Resource` entry differs between the access point and bucket policies\.   

```
{
    "Version": "2012-10-17",
    "Statement": [
    {
        "Effect": "Allow",
        "Principal": {
            "AWS": "arn:aws:iam::123456789012:user/Jane"
        },
        "Action": ["s3:GetObject", "s3:PutObject"],
        "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET1/Jane/*"
    }]    
}
```

**Example 2 – Access point policy with tag condition**  
The following access point policy grants IAM user *`Mateo`* in account *`123456789012`* permissions to `GET` objects through the access point *`my-access-point`* in the account *`123456789012`* that have the tag key *`data`* set with a value of *`finance`*\.  

```
{
    "Version":"2012-10-17",
    "Statement": [
    {
        "Effect":"Allow",
        "Principal" : {
            "AWS": "arn:aws:iam::123456789012:user/Mateo"
        },
        "Action":"s3:GetObject",
        "Resource" : "arn:aws:s3:us-west-2:123456789012:accesspoint/my-access-point/object/*",
        "Condition" : {
            "StringEquals": {
                "s3:ExistingObjectTag/data": "finance"
            }
        }
    }]
}
```

**Example 3 – Access point policy that allows bucket listing**  
The following access point policy allows IAM user `Arnav` in the account *`123456789012`* permission to view the objects contained in the bucket underlying the access point *`my-access-point`* in the account *`123456789012`*\.  

```
{
    "Version":"2012-10-17",
    "Statement": [
    {
        "Effect": "Allow",
        "Principal": {
            "AWS": "arn:aws:iam::123456789012:user/Arnav"
        },
        "Action": "s3:ListBucket",
        "Resource": "arn:aws:s3:us-west-2:123456789012:accesspoint/my-access-point"
    }]
}
```

**Example 4 – Service control policy**  
The following service control policy requires all new access points to be created with a virtual private cloud \(VPC\) network origin\. With this policy in place, users in your organization can't create new access points that are accessible from the internet\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
    {
        "Effect": "Deny",
        "Principal": "*",
        "Action": "s3:CreateAccessPoint",
        "Resource": "*",
        "Condition": {
            "StringNotEquals": {
                "s3:AccessPointNetworkOrigin": "VPC"
            }
        }
    }]
}
```

**Example 5 – Bucket policy to limit S3 operations to VPC network origins**  
The following bucket policy limits access to all S3 object operations for the bucket `DOC-EXAMPLE-BUCKET` to access points with a VPC network origin\.  
Before using a statement like the one shown in this example, make sure that you don't need to use features that aren't supported by access points, such as Cross\-Region Replication\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Deny",
            "Principal": "*",
            "Action": [
                "s3:AbortMultipartUpload",
                "s3:BypassGovernanceRetention",
                "s3:DeleteObject",
                "s3:DeleteObjectTagging",
                "s3:DeleteObjectVersion",
                "s3:DeleteObjectVersionTagging",
                "s3:GetObject",
                "s3:GetObjectAcl",
                "s3:GetObjectLegalHold",
                "s3:GetObjectRetention",
                "s3:GetObjectTagging",
                "s3:GetObjectVersion",
                "s3:GetObjectVersionAcl",
                "s3:GetObjectVersionTagging",
                "s3:ListMultipartUploadParts",
                "s3:PutObject",
                "s3:PutObjectAcl",
                "s3:PutObjectLegalHold",
                "s3:PutObjectRetention",
                "s3:PutObjectTagging",
                "s3:PutObjectVersionAcl",
                "s3:PutObjectVersionTagging",
                "s3:RestoreObject"
            ],
            "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*",
            "Condition": {
                "StringNotEquals": {
                    "s3:AccessPointNetworkOrigin": "VPC"
                }
            }
        }
    ]
}
```

## Condition keys<a name="access-points-condition-keys"></a>

S3 access points have condition keys that you can use in IAM policies to control access to your resources\. The following condition keys represent only part of an IAM policy\. For full policy examples, see [Access point policy examples](#access-points-policy-examples), [Delegating access control to access points](#access-points-delegating-control), and [Granting permissions for cross\-account access points](#access-points-cross-account)\. 

**`s3:DataAccessPointArn`**  
This example shows a string that you can use to match on an access point ARN\. The following example matches all access points for AWS account *`123456789012`* in Region *`us-west-2`*:  

```
"Condition" : {
    "StringLike": {
        "s3:DataAccessPointArn": "arn:aws:s3:us-west-2:123456789012:accesspoint/*"
    }
}
```

**`s3:DataAccessPointAccount`**  
This example shows a string operator that you can use to match on the account ID of the owner of an access point\. The following example matches all access points that are owned by the AWS account *`123456789012`*\.  

```
"Condition" : {
    "StringEquals": {
        "s3:DataAccessPointAccount": "123456789012"
    }
}
```

**`s3:AccessPointNetworkOrigin`**  
This example shows a string operator that you can use to match on the network origin, either `Internet` or `VPC`\. The following example matches only access points with a VPC origin\.  

```
"Condition" : {
    "StringEquals": {
        "s3:AccessPointNetworkOrigin": "VPC"
    }
}
```

For more information about using condition keys with Amazon S3, see [Actions, resources, and condition keys for Amazon S3](list_amazons3.md)\.

## Delegating access control to access points<a name="access-points-delegating-control"></a>

You can delegate access control for a bucket to the bucket's access points\. The following example bucket policy allows full access to all access points that are owned by the bucket owner's account\. Thus, all access to this bucket is controlled by the policies attached to its access points\. We recommend configuring your buckets this way for all use cases that don't require direct access to the bucket\.

**Example 6 – Bucket policy that delegates access control to access points**  

```
{
    "Version": "2012-10-17",
    "Statement" : [
    {
        "Effect": "Allow",
        "Principal" : { "AWS": "*" },
        "Action" : "*",
        "Resource" : [ "Bucket ARN", "Bucket ARN/*"],
        "Condition": {
            "StringEquals" : { "s3:DataAccessPointAccount" : "Bucket owner's account ID" }
        }
    }]
}
```

## Granting permissions for cross\-account access points<a name="access-points-cross-account"></a>

To create an access point to a bucket that's owned by another account, you must first create the access point by specifying the bucket name and account owner ID\. Then, the bucket owner must update the bucket policy to authorize requests from the access point\. Creating an access point is similar to creating a DNS CNAME in that the access point doesn't provide access to the bucket contents\. All bucket access is controlled by the bucket policy\. The following example bucket policy allows `GET` and `LIST` requests on the bucket from an access point that's owned by a trusted AWS account\.

**Example 7 – Bucket policy delegating permissions to another AWS account**  

```
{
    "Version": "2012-10-17",
    "Statement" : [
    {
        "Effect": "Allow",
        "Principal" : { "AWS": "*" },
        "Action" : ["s3:GetObject","s3:ListBucket"],
        "Resource" : [ "Bucket ARN", "Bucket ARN/*"],
        "Condition": {
            "StringEquals" : { "s3:DataAccessPointAccount" : "Access point owner's account ID" }
        }
    }]
}
```