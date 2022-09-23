# Configuring IAM policies for using access points<a name="access-points-policies"></a>

Amazon S3 access points support AWS Identity and Access Management \(IAM\) resource policies that allow you to control the use of the access point by resource, user, or other conditions\. For an application or user to be able to access objects through an access point, both the access point and the underlying bucket must permit the request\.

**Important**  
Adding an S3 access point to a bucket doesn't change the bucket's behavior when accessed through the existing bucket name or ARN\. All existing operations against the bucket will continue to work as before\. Restrictions that you include in an access point policy apply only to requests made through that access point\. 

## Access point policy examples<a name="access-points-policy-examples"></a>

The following examples demonstrate how to create IAM policies to control requests made through an access point\.

**Note**  
Permissions granted in an access point policy are only effective if the underlying bucket also allows the same access\. You can accomplish this in two ways:  
\(Recommended\) Delegate access control from the bucket to the access point as described in [Delegating access control to access points](#access-points-delegating-control)\.
Add the same permissions contained in the access point policy to the underlying bucket's policy\. The first access point policy example demonstrates how to modify the underlying bucket policy to allow the necessary access\.

**Example Access point policy grant**  
The following access point policy grants IAM user `Alice` in account `123456789012` permissions to `GET` and `PUT` objects with the prefix `Alice/` through access point `my-access-point` in account `123456789012`\.  

```
{
    "Version":"2012-10-17",
    "Statement": [
    {
        "Effect": "Allow",
        "Principal": {
            "AWS": "arn:aws:iam::123456789012:user/Alice"
        },
        "Action": ["s3:GetObject", "s3:PutObject"],
        "Resource": "arn:aws:s3:us-west-2:123456789012:accesspoint/my-access-point/object/Alice/*"
    }]
}
```

**Note**  
For the access point policy to effectively grant access to `Alice`, the underlying bucket must also allow the same access to `Alice`\. You can delegate access control from the bucket to the access point as described in [Delegating access control to access points](#access-points-delegating-control)\. Or, you can add the following policy to the underlying bucket to grant the necessary permissions to Alice\. Note that the `Resource` entry differs between the access point and bucket policies\.   

```
{
    "Version": "2012-10-17",
    "Statement": [
    {
        "Effect": "Allow",
        "Principal": {
            "AWS": "arn:aws:iam::123456789012:user/Alice"
        },
        "Action": ["s3:GetObject", "s3:PutObject"],
        "Resource": "arn:aws:s3:::awsexamplebucket1/Alice/*"
    }]    
}
```

**Example Access point policy with tag condition**  
The following access point policy grants IAM user `Bob` in account `123456789012` permissions to `GET` objects through access point `my-access-point` in account `123456789012` that have the tag key `data` set with a value of `finance`\.  

```
{
    "Version":"2012-10-17",
    "Statement": [
    {
        "Effect":"Allow",
        "Principal" : {
            "AWS": "arn:aws:iam::123456789012:user/Bob"
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

**Example Access point policy allowing bucket listing**  
The following access point policy allows IAM user `Charles` in account `123456789012` permission to view the objects contained in the bucket underlying access point `my-access-point` in account `123456789012`\.  

```
{
    "Version":"2012-10-17",
    "Statement": [
    {
        "Effect": "Allow",
        "Principal": {
            "AWS": "arn:aws:iam::123456789012:user/Charles"
        },
        "Action": "s3:ListBucket",
        "Resource": "arn:aws:s3:us-west-2:123456789012:accesspoint/my-access-point"
    }]
}
```

**Example Service control policy**  
The following service control policy requires all new access points to be created with a VPC network origin\. With this policy in place, users in your organization can't create new access points that are accessible from the internet\.  

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

**Example Bucket policy to limit S3 operations to VPC network origins**  
The following bucket policy limits access to all S3 object operations for bucket `examplebucket` to access points with a VPC network origin\.  
Before using a statement like this example, make sure you don't need to use features that aren't supported by access points, such as Cross\-Region Replication\.

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
            "Resource": "arn:aws:s3:::examplebucket/*",
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

S3 access points use condition keys that can be used in IAM policies to control access to your resources\. These condition keys are part of an IAM policies, for a full policy example see, [Access point policy examples](#access-points-policy-examples)\.

**s3:DataAccessPointArn**  
This is a string that you can use to match on an access point ARN\. The following example matches all access points for AWS account `123456789012` in Region `us-west-2`:  

```
"Condition" : {
    "StringLike": {
        "s3:DataAccessPointArn": "arn:aws:s3:us-west-2:123456789012:accesspoint/*"
    }
}
```

**s3:DataAccessPointAccount**  
This is a string operator that you can use to match on the account ID of the owner of an access point\. The following example matches all access points owned by AWS account `123456789012`\.  

```
"Condition" : {
    "StringEquals": {
        "s3:DataAccessPointAccount": "123456789012"
    }
}
```

**s3:AccessPointNetworkOrigin**  
This is a string operator that you can use to match on the network origin, either `Internet` or `VPC`\. The following example matches only access points with a VPC origin\.  

```
"Condition" : {
    "StringEquals": {
        "s3:AccessPointNetworkOrigin": "VPC"
    }
}
```

For more information about using condition keys with Amazon S3, see [Actions, resources, and condition keys for Amazon S3](list_amazons3.md)\.

## Delegating access control to access points<a name="access-points-delegating-control"></a>

You can delegate access control for a bucket to the bucket's access points\. The following example bucket policy allows full access to all access points owned by the bucket owner's account\. Thus, all access to this bucket is controlled by the policies attached to its access points\. We recommend configuring your buckets this way for all use cases that don't require direct access to the bucket\.

**Example Bucket policy delegating access control to access points**  

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