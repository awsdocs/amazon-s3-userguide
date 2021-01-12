--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Bucket policies and user policies<a name="using-iam-policies"></a>

Bucket policies and user policies are two access policy options available for granting permission to your Amazon S3 resources\. Both use JSON\-based access policy language\. 

The topics in this section describe the key policy language elements, with emphasis on Amazon S3–specific details, and provide example bucket and user policies\. We recommend that you first review the introductory topics that explain the basic concepts and options available for you to manage access to your Amazon S3 resources\. For more information, see [Identity and access management in Amazon S3](s3-access-control.md)\. 

**Important**  
Bucket policies are limited to 20 KB in size\.

In its most basic sense, a policy contains the following elements:
+ [Resources](s3-arn-format.md) – Buckets, objects, access points, and jobs are the Amazon S3 resources for which you can allow or deny permissions\. In a policy, you use the Amazon Resource Name \(ARN\) to identify the resource\. For more information, see [Amazon S3 resources](s3-arn-format.md)\.
+ [Actions](using-with-s3-actions.md) – For each resource, Amazon S3 supports a set of operations\. You identify resource operations that you will allow \(or deny\) by using action keywords\. 

  For example, the `s3:ListBucket` permission allows the user to use the Amazon S3 [GET Bucket \(List Objects\)](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGET.html) operation\. For more information, see [Amazon S3 actions](using-with-s3-actions.md)\.
+ [Effect](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_effect.html) – What the effect will be when the user requests the specific action—this can be either *allow* or *deny*\. 

  If you do not explicitly grant access to \(allow\) a resource, access is implicitly denied\. You can also explicitly deny access to a resource\. You might do this to make sure that a user can't access the resource, even if a different policy grants access\. For more information, see [IAM JSON Policy Elements: Effect](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_effect.html)\.
+ [Principal](s3-bucket-user-policy-specifying-principal-intro.md) – The account or user who is allowed access to the actions and resources in the statement\. In a bucket policy, the principal is the user, account, service, or other entity that is the recipient of this permission\. For more information, see [Principals](s3-bucket-user-policy-specifying-principal-intro.md)\.
+ [Condition](amazon-s3-policy-keys.md) – Conditions for when a policy is in effect\. You can use AWS‐wide keys and Amazon S3‐specific keys to specify conditions in an Amazon S3 access policy\. For more information, see [Amazon S3 condition keys](amazon-s3-policy-keys.md)\.

The following example bucket policy shows the preceding policy elements\. The policy allows Dave, a user in account *Account\-ID*, `s3:GetObject`, `s3:GetBucketLocation`, and `s3:ListBucket` Amazon S3 permissions on the `awsexamplebucket1` bucket\.

```
{
    "Version": "2012-10-17",
    "Id": "ExamplePolicy01",
    "Statement": [
        {
            "Sid": "ExampleStatement01",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::123456789012:user/Dave"
            },
            "Action": [
                "s3:GetObject",
                "s3:GetBucketLocation",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::awsexamplebucket1/*",
                "arn:aws:s3:::awsexamplebucket1"
            ]
        }
    ]
}
```

For complete policy language information, see [Policies and Permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html) and [IAM JSON Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

**Topics**
+ [Amazon S3 resources](s3-arn-format.md)
+ [Adding a bucket policy using the Amazon S3 console](add-bucket-policy.md)
+ [Controlling access from VPC endpoints with bucket policies](example-bucket-policies-vpc-endpoint.md)
+ [Controlling access to a bucket with user policies](walkthrough1.md)
+ [Bucket policy examples](example-bucket-policies.md)
+ [User policy examples](example-policies-s3.md)
+ [Using service\-linked roles for Amazon S3 Storage Lens](using-service-linked-roles.md)