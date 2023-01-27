# Blocking public access to your Amazon S3 storage<a name="access-control-block-public-access"></a>

The Amazon S3 Block Public Access feature provides settings for access points, buckets, and accounts to help you manage public access to Amazon S3 resources\. By default, new buckets, access points, and objects don't allow public access\. However, users can modify bucket policies, access point policies, or object permissions to allow public access\. S3 Block Public Access settings override these policies and permissions so that you can limit public access to these resources\. 

With S3 Block Public Access, account administrators and bucket owners can easily set up centralized controls to limit public access to their Amazon S3 resources that are enforced regardless of how the resources are created\.

For instructions on configuring public block access, see [Configuring block public access](#configuring-block-public-access)\.

**Important**  
Starting in April 2023, Amazon S3 will change the default settings for S3 Block Public Access and Object Ownership \(ACLs disabled\) for all new S3 buckets\. For new buckets created after this update, all S3 Block Public Access settings will be enabled, and S3 access control lists \(ACLs\) will be disabled\. These defaults are the recommended best practices for securing data in Amazon S3\. You can adjust these settings after creating your bucket\. For more information, see [ Default settings for new S3 buckets FAQ](create-bucket-faq.md) and [Heads\-Up: Amazon S3 Security Changes Are Coming in April of 2023](http://aws.amazon.com/blogs/aws/heads-up-amazon-s3-security-changes-are-coming-in-april-of-2023/) in the * AWS News Blog*\.

When Amazon S3 receives a request to access a bucket or an object, it determines whether the bucket or the bucket owner's account has a block public access setting applied\. If the request was made through an access point, Amazon S3 also checks for block public access settings for the access point\. If there is an existing block public access setting that prohibits the requested access, Amazon S3 rejects the request\. 

Amazon S3 Block Public Access provides four settings\. These settings are independent and can be used in any combination\. Each setting can be applied to an access point, a bucket, or an entire AWS account\. If the block public access settings for the access point, bucket, or account differ, then Amazon S3 applies the most restrictive combination of the access point, bucket, and account settings\. 

When Amazon S3 evaluates whether an operation is prohibited by a block public access setting, it rejects any request that violates an access point, bucket, or account setting\.

**Warning**  
Public access is granted to buckets and objects through access control lists \(ACLs\), access point policies, bucket policies, or all\. To help ensure that all of your Amazon S3 access points, buckets, and objects have their public access blocked, we recommend that you turn on all four settings for block public access for your account\. These settings block public access for all current and future buckets and access points\.   
Before applying these settings, verify that your applications will work correctly without public access\. If you require some level of public access to your buckets or objects, for example to host a static website as described at [Hosting a static website using Amazon S3](WebsiteHosting.md), you can customize the individual settings to suit your storage use cases\.

**Note**  
You can enable block public access settings only for access points, buckets, and AWS accounts\. Amazon S3 doesn't support block public access settings on a per\-object basis\.
When you apply block public access settings to an account, the settings apply to all AWS Regions globally\. The settings might not take effect in all Regions immediately or simultaneously, but they eventually propagate to all Regions\.

**Topics**
+ [Block public access settings](#access-control-block-public-access-options)
+ [Performing block public access operations on an access point](#access-control-block-public-access-examples-access-point)
+ [The meaning of "public"](#access-control-block-public-access-policy-status)
+ [Using Access Analyzer for S3 to review public buckets](#access-analyzer-public-info)
+ [Permissions](#access-control-block-public-access-permissions)
+ [Configuring block public access](#configuring-block-public-access)
+ [Configuring block public access settings for your account](configuring-block-public-access-account.md)
+ [Configuring block public access settings for your S3 buckets](configuring-block-public-access-bucket.md)

## Block public access settings<a name="access-control-block-public-access-options"></a>

S3 Block Public Access provides four settings\. You can apply these settings in any combination to individual access points, buckets, or entire AWS accounts\. If you apply a setting to an account, it applies to all buckets and access points that are owned by that account\. Similarly, if you apply a setting to a bucket, it applies to all access points associated with that bucket\.

The following table contains the available settings\.


| Name | Description | 
| --- | --- | 
| BlockPublicAcls |  Setting this option to `TRUE` causes the following behavior: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/access-control-block-public-access.html) When this setting is set to `TRUE`, the specified operations fail \(whether made through the REST API, AWS CLI, or AWS SDKs\)\. However, existing policies and ACLs for buckets and objects are not modified\. This setting enables you to protect against public access while allowing you to audit, refine, or otherwise alter the existing policies and ACLs for your buckets and objects\.  Access points don't have ACLs associated with them\. If you apply this setting to an access point, it acts as a passthrough to the underlying bucket\. If an access point has this setting enabled, requests made through the access point behave as though the underlying bucket has this setting enabled, regardless of whether the bucket actually has this setting enabled\.   | 
| IgnorePublicAcls |  Setting this option to `TRUE` causes Amazon S3 to ignore all public ACLs on a bucket and any objects that it contains\. This setting enables you to safely block public access granted by ACLs while still allowing PUT Object calls that include a public ACL \(as opposed to `BlockPublicAcls`, which rejects PUT Object calls that include a public ACL\)\. Enabling this setting doesn't affect the persistence of any existing ACLs and doesn't prevent new public ACLs from being set\.  Access points don't have ACLs associated with them\. If you apply this setting to an access point, it acts as a passthrough to the underlying bucket\. If an access point has this setting enabled, requests made through the access point behave as though the underlying bucket has this setting enabled, regardless of whether the bucket actually has this setting enabled\.   | 
| BlockPublicPolicy |  Setting this option to `TRUE` for a bucket causes Amazon S3 to reject calls to PUT Bucket policy if the specified bucket policy allows public access\. Setting this option to `TRUE` for a bucket also causes Amazon S3 to reject calls to PUT access point policy for all of the bucket's same\-account access points if the specified policy allows public access\.  Setting this option to `TRUE` for an access point causes Amazon S3 to reject calls to PUT access point policy and PUT Bucket policy that are made through the access point if the specified policy \(for either the access point or the underlying bucket\) allows public access\. You can use this setting to allow users to manage access point and bucket policies without allowing them to publicly share the bucket or the objects it contains\. Enabling this setting doesn't affect existing access point or bucket policies\.  To use this setting effectively, we recommend that you apply it at the *account* level\. A bucket policy can allow users to alter a bucket's block public access settings\. Therefore, users who have permission to change a bucket policy could insert a policy that allows them to disable the block public access settings for the bucket\. If this setting is enabled for the entire account, rather than for a specific bucket, Amazon S3 blocks public policies even if a user alters the bucket policy to disable this setting\.   | 
| RestrictPublicBuckets |  Setting this option to `TRUE` restricts access to an access point or bucket with a public policy to only AWS service principals and authorized users within the bucket owner's account and access point owner's account\. This setting blocks all cross\-account access to the access point or bucket \(except by AWS service principals\), while still allowing users within the account to manage the access point or bucket\. Enabling this setting doesn't affect existing access point or bucket policies, except that Amazon S3 blocks public and cross\-account access derived from any public access point or bucket policy, including non\-public delegation to specific accounts\.  | 

**Important**  
Calls to GET Bucket acl and GET Object acl always return the effective permissions in place for the specified bucket or object\. For example, suppose that a bucket has an ACL that grants public access, but the bucket also has the `IgnorePublicAcls` setting enabled\. In this case, GET Bucket acl returns an ACL that reflects the access permissions that Amazon S3 is enforcing, rather than the actual ACL that is associated with the bucket\.
Block public access settings don't alter existing policies or ACLs\. Therefore, removing a block public access setting causes a bucket or object with a public policy or ACL to again be publicly accessible\. 

## Performing block public access operations on an access point<a name="access-control-block-public-access-examples-access-point"></a>

To perform block public access operations on an access point, use the AWS CLI service `s3control`\. 

**Important**  
Note that it isn't currently possible to change an access point's block public access settings after creating the access point\. Thus, the only way to specify block public access settings for an access point is by including them when creating the access point\.

## The meaning of "public"<a name="access-control-block-public-access-policy-status"></a>

### ACLs<a name="public-acls"></a>

Amazon S3 considers a bucket or object ACL public if it grants any permissions to members of the predefined `AllUsers` or `AuthenticatedUsers` groups\. For more information about predefined groups, see [Amazon S3 predefined groups](acl-overview.md#specifying-grantee-predefined-groups)\.

### Bucket policies<a name="public-bucket-policies"></a>

When evaluating a bucket policy, Amazon S3 begins by assuming that the policy is public\. It then evaluates the policy to determine whether it qualifies as non\-public\. To be considered non\-public, a bucket policy must grant access only to fixed values \(values that don't contain a wildcard or [an AWS Identity and Access Management Policy Variable](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_variables.html)\) for one or more of the following:
+ An AWS principal, user, role, or service principal \(e\.g\. `aws:PrincipalOrgID`\)
+ A set of Classless Inter\-Domain Routings \(CIDRs\), using `aws:SourceIp`\. For more information about CIDR, see [RFC 4632](http://www.rfc-editor.org/rfc/rfc4632.txt) on the RFC Editor website\.
**Note**  
Bucket policies that grant access conditioned on the `aws:SourceIp` condition key with very broad IP ranges \(for example, 0\.0\.0\.0/1\) are evaluated as "public\." This includes values broader than /8 for IPv4 and /32 for IPv6 \(excluding RFC1918 private ranges\)\. Block public access will reject these "public" policies and prevent cross\-account access to buckets already using these "public" policies\.
+ `aws:SourceArn`
+ `aws:SourceVpc`
+ `aws:SourceVpce`
+ `aws:SourceOwner`
+ `aws:SourceAccount`
+ `s3:x-amz-server-side-encryption-aws-kms-key-id`
+ `aws:userid`, outside the pattern "`AROLEID:*`"
+ `s3:DataAccessPointArn`
**Note**  
When used in a bucket policy, this value can contain a wildcard for the access point name without rendering the policy public, as long as the account id is fixed\. For example, allowing access to `arn:aws:s3:us-west-2:123456789012:accesspoint/*` would permit access to any access point associated with account `123456789012` in Region `us-west-2`, without rendering the bucket policy public\. Note that this behavior is different for access point policies\. For more information, see [Access points](#access-control-block-public-access-policy-status-access-points)\.
+ `s3:DataAccessPointAccount`

For more information about bucket policies, see [Bucket policies and user policies](using-iam-policies.md)\.

**Example : Public bucket policies**  
Under these rules, the following example policies are considered public\.  

```
{ 
		"Principal": { "Federated": "graph.facebook.com" }, 
		"Resource": "*", 
		"Action": "s3:PutObject", 
		"Effect": "Allow"
	}
```

```
{
		"Principal": "*", 
		"Resource": "*", 
		"Action": "s3:PutObject", 
		"Effect": "Allow" 
	}
```

```
{
		"Principal": "*", 
		"Resource": "*", 
		"Action": "s3:PutObject", 
		"Effect": "Allow", 
		"Condition": { "StringLike": {"aws:SourceVpc": "vpc-*"}}
	}
```
You can make these policies non\-public by including any of the condition keys listed previously, using a fixed value\. For example, you can make the last policy preceding non\-public by setting `aws:SourceVpc` to a fixed value, like the following\.  

```
{
		"Principal": "*", 
		"Resource": "*", 
		"Action": "s3:PutObject", 
		"Effect": "Allow", 
		"Condition": {"StringEquals": {"aws:SourceVpc": "vpc-91237329"}}
	}
```

### How Amazon S3 evaluates a bucket policy that contains both public and non\-public access grants<a name="access-control-block-public-access-policy-example"></a>

This example shows how Amazon S3 evaluates a bucket policy that contains both public and non\-public access grants\.

Suppose that a bucket has a policy that grants access to a set of fixed principals\. Under the previously described rules, this policy isn't public\. Thus, if you enable the `RestrictPublicBuckets` setting, the policy remains in effect as written, because `RestrictPublicBuckets` only applies to buckets that have public policies\. However, if you add a public statement to the policy, `RestrictPublicBuckets` takes effect on the bucket\. It allows only AWS service principals and authorized users of the bucket owner's account to access the bucket\.

As an example, suppose that a bucket owned by "Account\-1" has a policy that contains the following:

1. A statement that grants access to AWS CloudTrail \(which is an AWS service principal\)

1. A statement that grants access to account "Account\-2"

1. A statement that grants access to the public, for example by specifying `"Principal": "*"` with no limiting `Condition`

This policy qualifies as public because of the third statement\. With this policy in place and `RestrictPublicBuckets` enabled, Amazon S3 allows access only by CloudTrail\. Even though statement 2 isn't public, Amazon S3 disables access by "Account\-2\." This is because statement 3 renders the entire policy public, so `RestrictPublicBuckets` applies\. As a result, Amazon S3 disables cross\-account access, even though the policy delegates access to a specific account, "Account\-2\." But if you remove statement 3 from the policy, then the policy doesn't qualify as public, and `RestrictPublicBuckets` no longer applies\. Thus, "Account\-2" regains access to the bucket, even if you leave `RestrictPublicBuckets` enabled\.

### Access points<a name="access-control-block-public-access-policy-status-access-points"></a>

Amazon S3 evaluates block public access settings slightly differently for access points compared to buckets\. The rules that Amazon S3 applies to determine when an access point policy is public are generally the same for access points as for buckets, except in the following situations:
+ An access point that has a VPC network origin is always considered non\-public, regardless of the contents of its access point policy\.
+ An access point policy that grants access to a set of access points using `s3:DataAccessPointArn` is considered public\. Note that this behavior is different than for bucket policies\. For example, a bucket policy that grants access to values of `s3:DataAccessPointArn` that match `arn:aws:s3:us-west-2:123456789012:accesspoint/*` is not considered public\. However, the same statement in an access point policy would render the access point public\.

## Using Access Analyzer for S3 to review public buckets<a name="access-analyzer-public-info"></a>

You can use Access Analyzer for S3 to review buckets with bucket ACLs, bucket policies, or access point policies that grant public access\. Access Analyzer for S3 alerts you to buckets that are configured to allow access to anyone on the internet or other AWS accounts, including AWS accounts outside of your organization\. For each public or shared bucket, you receive findings that report the source and level of public or shared access\. 

In Access Analyzer for S3, you can block all public access to a bucket with a single click\. You can also drill down into bucket\-level permission settings to configure granular levels of access\. For specific and verified use cases that require public or shared access, you can acknowledge and record your intent for the bucket to remain public or shared by archiving the findings for the bucket\.

In rare events, Access Analyzer for S3 might report no findings for a bucket that an Amazon S3 block public access evaluation reports as public\. This happens because Amazon S3 block public access reviews policies for current actions and any potential actions that might be added in the future, leading to a bucket becoming public\. On the other hand, Access Analyzer for S3 only analyzes the current actions specified for the Amazon S3 service in the evaluation of access status\.

For more information about Access Analyzer for S3, see [Reviewing bucket access using Access Analyzer for S3](access-analyzer.md)\.

## Permissions<a name="access-control-block-public-access-permissions"></a>

To use Amazon S3 Block Public Access features, you must have the following permissions\.


| Operation | Required permissions | 
| --- | --- | 
| GET bucket policy status | s3:GetBucketPolicyStatus | 
| GET bucket Block Public Access settings | s3:GetBucketPublicAccessBlock | 
| PUT bucket Block Public Access settings | s3:PutBucketPublicAccessBlock | 
| DELETE bucket Block Public Access settings | s3:PutBucketPublicAccessBlock | 
| GET account Block Public Access settings | s3:GetAccountPublicAccessBlock | 
| PUT account Block Public Access settings | s3:PutAccountPublicAccessBlock | 
| DELETE account Block Public Access settings | s3:PutAccountPublicAccessBlock | 
| PUT access point Block Public Access settings | s3:CreateAccessPoint | 

**Note**  
The DELETE operations require the same permissions as the PUT operations\. There are no separate permissions for the DELETE operations\.

## Configuring block public access<a name="configuring-block-public-access"></a>

For more information about configuring block public access for your AWS account and your Amazon S3 buckets, see the following topics\.
+ [Configuring block public access settings for your account](configuring-block-public-access-account.md)
+ [Configuring block public access settings for your S3 buckets](configuring-block-public-access-bucket.md)