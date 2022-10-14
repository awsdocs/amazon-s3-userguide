# Bucket policy examples<a name="example-bucket-policies"></a>

This section presents a few examples of typical use cases for bucket policies\. The policies use *DOC\-EXAMPLE\-BUCKET* strings in the resource value\. To test these policies, replace these strings with your bucket name\. For information about bucket policies, see [Using bucket policies](bucket-policies.md)\. For more information about policy language, see [Policies and Permissions in Amazon S3](access-policy-language-overview.md)\.

A bucket policy is a resource\-based policy that you can use to grant access permissions to your bucket and the objects in it\. Only the bucket owner can associate a policy with a bucket\. The permissions attached to the bucket apply to all of the objects in the bucket that are owned by the bucket owner\. These permissions do not apply to objects owned by other AWS accounts\.

By default, when another AWS account uploads an object to your S3 bucket, that account \(the object writer\) owns the object, has access to it, and can grant other users access to it through access control lists \(ACLs\)\. You can use Object Ownership to change this default behavior so that ACLs are disabled and you, as the bucket owner, automatically own every object in your bucket\. As a result, access control for your data is based on policies, such as IAM policies, S3 bucket policies, virtual private cloud \(VPC\) endpoint policies, and AWS Organizations service control policies \(SCPs\)\. For more information, see [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\.

For more information about bucket policies, see [Using bucket policies](bucket-policies.md)\.

**Note**  
Bucket policies are limited to 20 KB in size\.

You can use the [AWS Policy Generator](http://aws.amazon.com/blogs/aws/aws-policy-generator/) to create a bucket policy for your Amazon S3 bucket\. You can then use the generated document to set your bucket policy by using the [Amazon S3 console](https://console.aws.amazon.com/s3/home), through several third\-party tools, or through your application\. 

**Important**  
When testing permissions by using the Amazon S3 console, you must grant additional permissions that the console requires—`s3:ListAllMyBuckets`, `s3:GetBucketLocation`, and `s3:ListBucket`\. For an example walkthrough that grants permissions to users and tests them by using the console, see [Controlling access to a bucket with user policies](walkthrough1.md)\.

**Topics**
+ [Granting permissions to multiple accounts with added conditions](#example-bucket-policies-use-case-1)
+ [Granting read\-only permission to an anonymous user](#example-bucket-policies-use-case-2)
+ [Limiting access to specific IP addresses](#example-bucket-policies-use-case-3)
+ [Restricting access to a specific HTTP referer](#example-bucket-policies-use-case-4)
+ [Granting permission to an Amazon CloudFront OAI](#example-bucket-policies-cloudfront)
+ [Adding a bucket policy to require MFA](#example-bucket-policies-use-case-7)
+ [Granting cross\-account permissions to upload objects while ensuring the bucket owner has full control](#example-bucket-policies-use-case-8)
+ [Granting permissions for Amazon S3 Inventory and Amazon S3 analytics](#example-bucket-policies-use-case-9)
+ [Restricting access to an Amazon S3 Inventory report](#example-bucket-policies-use-case-10)
+ [Granting permissions for Amazon S3 Storage Lens](#example-bucket-policies-lens)

## Granting permissions to multiple accounts with added conditions<a name="example-bucket-policies-use-case-1"></a>

The following example policy grants the `s3:PutObject` and `s3:PutObjectAcl` permissions to multiple AWS accounts and requires that any request for these operations include the `public-read` canned access control list \(ACL\)\. For more information, see [Amazon S3 actions](using-with-s3-actions.md) and [Amazon S3 condition key examples](amazon-s3-policy-keys.md)\.

**Warning**  
Use caution when granting anonymous access to your Amazon S3 bucket or disabling block public access settings\. When you grant anonymous access, anyone in the world can access your bucket\. We recommend that you never grant anonymous access to your Amazon S3 bucket unless you specifically need to, such as with [static website hosting](WebsiteHosting.md)\.

```
 1. {
 2.     "Version": "2012-10-17",
 3.     "Statement": [
 4.         {
 5.             "Sid": "AddCannedAcl",
 6.             "Effect": "Allow",
 7.             "Principal": {
 8.                 "AWS": [
 9.                     "arn:aws:iam::111122223333:root",
10.                     "arn:aws:iam::444455556666:root"
11.                 ]
12.             },
13.             "Action": [
14.                 "s3:PutObject",
15.                 "s3:PutObjectAcl"
16.             ],
17.             "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*",
18.             "Condition": {
19.                 "StringEquals": {
20.                     "s3:x-amz-acl": [
21.                         "public-read"
22.                     ]
23.                 }
24.             }
25.         }
26.     ]
27. }
```

## Granting read\-only permission to an anonymous user<a name="example-bucket-policies-use-case-2"></a>

The following example policy grants the `s3:GetObject` permission to any public anonymous users\. \(For a list of permissions and the operations that they allow, see [Amazon S3 actions](using-with-s3-actions.md)\.\) This permission allows anyone to read the object data, which is useful if you configure your bucket as a website and want everyone to be able to read objects in the bucket\. Before you use a bucket policy to grant read\-only permission to an anonymous user, you must disable block public access settings for your bucket\. For more information, see [Setting permissions for website access](WebsiteAccessPermissionsReqd.md)\.

**Warning**  
Use caution when granting anonymous access to your Amazon S3 bucket or disabling block public access settings\. When you grant anonymous access, anyone in the world can access your bucket\. We recommend that you never grant anonymous access to your Amazon S3 bucket unless you specifically need to, such as with [static website hosting](WebsiteHosting.md)\.

```
 1. {
 2.     "Version": "2012-10-17",
 3.     "Statement": [
 4.         {
 5.             "Sid": "PublicRead",
 6.             "Effect": "Allow",
 7.             "Principal": "*",
 8.             "Action": [
 9.                 "s3:GetObject",
10.                 "s3:GetObjectVersion"
11.             ],
12.             "Resource": [
13.                 "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*"
14.             ]
15.         }
16.     ]
17. }
```

## Limiting access to specific IP addresses<a name="example-bucket-policies-use-case-3"></a>

The following example denies permissions to any user to perform any Amazon S3 operations on objects in the specified S3 bucket unless the request originates from the range of IP addresses that are specified in the condition\. 

This statement identifies *`54.240.143.0/24`* as the range of allowed Internet Protocol version 4 \(IPv4\) IP addresses\. 

The `Condition` block uses the `NotIpAddress` condition and the `aws:SourceIp` condition key, which is an AWS wide condition key\. For more information about these condition keys, see [Amazon S3 condition key examples](amazon-s3-policy-keys.md)\. The `aws:SourceIp` IPv4 values use standard CIDR notation\. For more information, see [IAM JSON Policy Elements Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#Conditions_IPAddress) in the *IAM User Guide*\. 

**Warning**  
Before using this policy, replace the *`54.240.143.0/24`* IP address range in this example with an appropriate value for your use case\. Otherwise, you will lose the ability to access your bucket\.

```
 1. {
 2.     "Version": "2012-10-17",
 3.     "Id": "S3PolicyId1",
 4.     "Statement": [
 5.         {
 6.             "Sid": "IPAllow",
 7.             "Effect": "Deny",
 8.             "Principal": "*",
 9.             "Action": "s3:*",
10.             "Resource": [
11.                 "arn:aws:s3:::DOC-EXAMPLE-BUCKET",
12.                 "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*"
13.             ],
14.             "Condition": {
15.                 "NotIpAddress": {
16.                     "aws:SourceIp": "54.240.143.0/24"
17.                 }
18.             }
19.         }
20.     ]
21. }
```

### Allowing IPv4 and IPv6 addresses<a name="example-bucket-policies-use-case-ipv6"></a>

When you start using IPv6 addresses, we recommend that you update all of your organization's policies with your IPv6 address ranges in addition to your existing IPv4 ranges to ensure that the policies continue to work as you make the transition to IPv6\.

The following example bucket policy shows how to mix IPv4 and IPv6 address ranges to cover all of your organization's valid IP addresses\. The example policy allows access to the example IP addresses *`54.240.143.1`* and *`2001:DB8:1234:5678::1`* and denies access to the addresses *`54.240.143.129`* and *`2001:DB8:1234:5678:ABCD::1`*\.

The IPv6 values for `aws:SourceIp` must be in standard CIDR format\. For IPv6, we support using `::` to represent a range of 0s \(for example, `2032001:DB8:1234:5678::/64`\)\. For more information, see [ IP Address Condition Operators](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html#Conditions_IPAddress) in the *IAM User Guide*\.

**Warning**  
Replace the IP address ranges in this example with appropriate values for your use case before using this policy\. Otherwise, you might lose the ability to access your bucket\.

```
 1. {
 2.     "Id": "PolicyId2",
 3.     "Version": "2012-10-17",
 4.     "Statement": [
 5.         {
 6.             "Sid": "AllowIPmix",
 7.             "Effect": "Allow",
 8.             "Principal": "*",
 9.             "Action": "s3:*",
10.             "Resource": [
11.                 "arn:aws:s3:::DOC-EXAMPLE-BUCKET",
12.                 "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*"
13.             ],
14.             "Condition": {
15.                 "IpAddress": {
16.                     "aws:SourceIp": [
17.                         "54.240.143.0/24",
18.                         "2001:DB8:1234:5678::/64"
19.                     ]
20.                 },
21.                 "NotIpAddress": {
22.                     "aws:SourceIp": [
23.                         "54.240.143.128/30",
24.                         "2001:DB8:1234:5678:ABCD::/80"
25.                     ]
26.                 }
27.             }
28.         }
29.     ]
30. }
```

## Restricting access to a specific HTTP referer<a name="example-bucket-policies-use-case-4"></a>

Suppose that you have a website with a domain name \(*`www.example.com`* or *`example.com`*\) with links to photos and videos stored in your Amazon S3 bucket, `DOC-EXAMPLE-BUCKET`\. By default, all Amazon S3 resources are private, so only the AWS account that created the resources can access them\. To allow read access to these objects from your website, you can add a bucket policy that allows the `s3:GetObject` permission with a condition, using the `aws:Referer` key, that the `GET` request must originate from specific webpages\. The following policy specifies the `StringLike` condition with the `aws:Referer` condition key\.

```
 1. {
 2.   "Version":"2012-10-17",
 3.   "Id":"HTTP referer policy example",
 4.   "Statement":[
 5.     {
 6.       "Sid":"Allow GET requests originating from www.example.com and example.com.",
 7.       "Effect":"Allow",
 8.       "Principal":"*",
 9.       "Action":["s3:GetObject","s3:GetObjectVersion"],
10.       "Resource":"arn:aws:s3:::DOC-EXAMPLE-BUCKET/*",
11.       "Condition":{
12.         "StringLike":{"aws:Referer":["http://www.example.com/*","http://example.com/*"]}
13.       }
14.     }
15.   ]
16. }
```

Make sure that the browsers that you use include the HTTP `referer` header in the request\.

**Warning**  
We recommend that you use caution when using the `aws:Referer` condition key\. It is dangerous to include a publicly known HTTP referer header value\. Unauthorized parties can use modified or custom browsers to provide any `aws:Referer` value that they choose\. Therefore, do not use `aws:Referer` to prevent unauthorized parties from making direct AWS requests\.   
The `aws:Referer` condition key is offered only to allow customers to protect their digital content, such as content stored in Amazon S3, from being referenced on unauthorized third\-party sites\. For more information, see [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-referer](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-referer) in the *IAM User Guide*\.

## Granting permission to an Amazon CloudFront OAI<a name="example-bucket-policies-cloudfront"></a>

The following example bucket policy grants a CloudFront origin access identity \(OAI\) permission to get \(read\) all objects in your Amazon S3 bucket\. You can use a CloudFront OAI to allow users to access objects in your bucket through CloudFront but not directly through Amazon S3\. For more information, see [Restricting Access to Amazon S3 Content by Using an Origin Access Identity](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html) in the *Amazon CloudFront Developer Guide*\.

The following policy uses the OAI’s ID as the policy’s `Principal`\. For more information about using S3 bucket policies to grant access to a CloudFront OAI, see [Using Amazon S3 Bucket Policies](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html#private-content-updating-s3-bucket-policies) in the *Amazon CloudFront Developer Guide*\.

To use this example:
+ Replace `EH1HDMB1FH2TC` with the OAI’s ID\. To find the OAI’s ID, see the [Origin Access Identity page](https://console.aws.amazon.com/cloudfront/home?region=us-east-1#oai:) on the CloudFront console, or use [https://docs.aws.amazon.com/cloudfront/latest/APIReference/API_ListCloudFrontOriginAccessIdentities.html](https://docs.aws.amazon.com/cloudfront/latest/APIReference/API_ListCloudFrontOriginAccessIdentities.html) in the CloudFront API\.
+ Replace `DOC-EXAMPLE-BUCKET` with the name of your Amazon S3 bucket\.

```
 1. {
 2.     "Version": "2012-10-17",
 3.     "Id": "PolicyForCloudFrontPrivateContent",
 4.     "Statement": [
 5.         {
 6.             "Effect": "Allow",
 7.             "Principal": {
 8.                 "AWS": "arn:aws:iam::cloudfront:user/CloudFront Origin Access Identity EH1HDMB1FH2TC"
 9.             },
10.             "Action": "s3:GetObject",
11.             "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*"
12.         }
13.     ]
14. }
```

## Adding a bucket policy to require MFA<a name="example-bucket-policies-use-case-7"></a>

Amazon S3 supports MFA\-protected API access, a feature that can enforce multi\-factor authentication \(MFA\) for access to your Amazon S3 resources\. Multi\-factor authentication provides an extra level of security that you can apply to your AWS environment\. MFA is a security feature that requires users to prove physical possession of an MFA device by providing a valid MFA code\. For more information, see [AWS Multi\-Factor Authentication](https://aws.amazon.com/mfa/)\. You can require MFA for any requests to access your Amazon S3 resources\. 

To enforce the MFA requirement, use the `aws:MultiFactorAuthAge` key in a bucket policy\. AWS Identity and Access Management \(IAM\) users can access Amazon S3 resources by using temporary credentials issued by the AWS Security Token Service \(AWS STS\)\. You provide the MFA code at the time of the AWS STS request\. 

When Amazon S3 receives a request with multi\-factor authentication, the `aws:MultiFactorAuthAge` key provides a numeric value that indicates how long ago \(in seconds\) the temporary credential was created\. If the temporary credential provided in the request was not created by using an MFA device, this key value is null \(absent\)\. In a bucket policy, you can add a condition to check this value, as shown in the following example\. This example policy denies any Amazon S3 operation on the *`/taxdocuments`* folder in the `DOC-EXAMPLE-BUCKET` bucket if the request is not authenticated by using MFA\. To learn more about MFA, see [Using Multi\-Factor Authentication \(MFA\) in AWS](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa.html) in the *IAM User Guide*\.

```
 1. {
 2.     "Version": "2012-10-17",
 3.     "Id": "123",
 4.     "Statement": [
 5.       {
 6.         "Sid": "",
 7.         "Effect": "Deny",
 8.         "Principal": "*",
 9.         "Action": "s3:*",
10.         "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/taxdocuments/*",
11.         "Condition": { "Null": { "aws:MultiFactorAuthAge": true }}
12.       }
13.     ]
14.  }
```

The `Null` condition in the `Condition` block evaluates to `true` if the `aws:MultiFactorAuthAge` key value is null, indicating that the temporary security credentials in the request were created without an MFA device\. 

The following bucket policy is an extension of the preceding bucket policy\. It includes two policy statements\. One statement allows the `s3:GetObject` permission on a bucket \(`DOC-EXAMPLE-BUCKET`\) to everyone\. Another statement further restricts access to the `DOC-EXAMPLE-BUCKET/taxdocuments` folder in the bucket by requiring MFA\. 

```
 1. {
 2.     "Version": "2012-10-17",
 3.     "Id": "123",
 4.     "Statement": [
 5.       {
 6.         "Sid": "",
 7.         "Effect": "Deny",
 8.         "Principal": "*",
 9.         "Action": "s3:*",
10.         "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/taxdocuments/*",
11.         "Condition": { "Null": { "aws:MultiFactorAuthAge": true } }
12.       },
13.       {
14.         "Sid": "",
15.         "Effect": "Allow",
16.         "Principal": "*",
17.         "Action": ["s3:GetObject"],
18.         "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*"
19.       }
20.     ]
21.  }
```

You can optionally use a numeric condition to limit the duration for which the `aws:MultiFactorAuthAge` key is valid, independent of the lifetime of the temporary security credential that's used in authenticating the request\. For example, the following bucket policy, in addition to requiring MFA authentication, also checks how long ago the temporary session was created\. The policy denies any operation if the `aws:MultiFactorAuthAge` key value indicates that the temporary session was created more than an hour ago \(3,600 seconds\)\. 

```
 1. {
 2.     "Version": "2012-10-17",
 3.     "Id": "123",
 4.     "Statement": [
 5.       {
 6.         "Sid": "",
 7.         "Effect": "Deny",
 8.         "Principal": "*",
 9.         "Action": "s3:*",
10.         "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/taxdocuments/*",
11.         "Condition": {"Null": {"aws:MultiFactorAuthAge": true }}
12.       },
13.       {
14.         "Sid": "",
15.         "Effect": "Deny",
16.         "Principal": "*",
17.         "Action": "s3:*",
18.         "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/taxdocuments/*",
19.         "Condition": {"NumericGreaterThan": {"aws:MultiFactorAuthAge": 3600 }}
20.        },
21.        {
22.          "Sid": "",
23.          "Effect": "Allow",
24.          "Principal": "*",
25.          "Action": ["s3:GetObject"],
26.          "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*"
27.        }
28.     ]
29.  }
```

## Granting cross\-account permissions to upload objects while ensuring the bucket owner has full control<a name="example-bucket-policies-use-case-8"></a>

The following example shows how to allow another AWS account to upload objects to your bucket while ensuring that you have full control of the uploaded objects\. This policy grants a specific AWS account \(*`111122223333`*\) the ability to upload objects only if that account includes the `bucket-owner-full-control` canned ACL on upload\. The `StringEquals` condition in the policy specifies the `s3:x-amz-acl condition` key to express the requirement \(see [Amazon S3 condition key examples](amazon-s3-policy-keys.md)\)\. 

```
 1. {
 2.    "Version":"2012-10-17",
 3.    "Statement":[
 4.      {
 5.        "Sid":"PolicyForAllowUploadWithACL",
 6.        "Effect":"Allow",
 7.        "Principal":{"AWS":"111122223333"},
 8.        "Action":"s3:PutObject",
 9.        "Resource":"arn:aws:s3:::DOC-EXAMPLE-BUCKET/*",
10.        "Condition": {
11.          "StringEquals": {"s3:x-amz-acl":"bucket-owner-full-control"}
12.        }
13.      }
14.    ]
15. }
```

## Granting permissions for Amazon S3 Inventory and Amazon S3 analytics<a name="example-bucket-policies-use-case-9"></a>

Amazon S3 Inventory creates lists of the objects in an Amazon S3 bucket, and Amazon S3 analytics export creates output files of the data used in the analysis\. The bucket that the inventory lists the objects for is called the *source bucket*\. The bucket where the inventory file or the analytics export file is written to is called a *destination bucket*\. When setting up an inventory or an analytics export, you must create a bucket policy for the destination bucket\. For more information, see [Amazon S3 Inventory](storage-inventory.md) and [Amazon S3 analytics – Storage Class Analysis](analytics-storage-class.md)\.

The following example bucket policy grants Amazon S3 permission to write objects \(`PUT` requests\) from the account for the source bucket to the destination bucket\. You use a bucket policy like this on the destination bucket when setting up Amazon S3 Inventory and Amazon S3 analytics export\.

```
 1. {  
 2.       "Version": "2012-10-17",
 3.       "Statement": [
 4.         {
 5.             "Sid": "InventoryAndAnalyticsExamplePolicy",
 6.             "Effect": "Allow",
 7.             "Principal": {
 8.                 "Service": "s3.amazonaws.com"
 9.             },
10.             "Action": "s3:PutObject",
11.             "Resource": [
12.                 "arn:aws:s3:::destinationbucket/*"
13.             ],
14.             "Condition": {
15.                 "ArnLike": {
16.                     "aws:SourceArn": "arn:aws:s3:::sourcebucket"
17.                 },
18.                 "StringEquals": {
19.                     "aws:SourceAccount": "111122223333",
20.                     "s3:x-amz-acl": "bucket-owner-full-control"
21.                 }
22.             }
23.         }
24.     ]
25. }
```

## Restricting access to an Amazon S3 Inventory report<a name="example-bucket-policies-use-case-10"></a>

[Amazon S3 Inventory](storage-inventory.md) creates lists of the objects in an S3 bucket and the metadata for each object\. The `s3:PutInventoryConfiguration` permission allows a user to create an inventory report that includes all object metadata fields that are available and to specify the destination bucket to store the inventory\. A user with read access to objects in the destination bucket can access all object metadata fields that are available in the inventory report\. For more information about the metadata fields that are available in S3 Inventory, see [Amazon S3 Inventory list](storage-inventory.md#storage-inventory-contents)\.

To restrict a user from configuring an S3 Inventory report of all object metadata available, remove the `s3:PutInventoryConfiguration` permission from the user\.

To restrict a user from accessing your S3 Inventory report in a destination bucket, create a bucket policy like the following example on the destination bucket\. This example bucket policy denies all the principals except the user *Ana* from accessing the inventory report *DOC\-EXAMPLE\-DESTINATION\-BUCKET\-INVENTORY* in the destination bucket *DOC\-EXAMPLE\-DESTINATION\-BUCKET*\.

```
 1. {
 2. 	"Id": "GetObjectPolicy",
 3. 	"Version": "2012-10-17",
 4. 	"Statement": [{
 5. 			"Sid": "AllowListBucket",
 6. 			"Action": [
 7. 				"s3:ListBucket"
 8. 			],
 9. 			"Effect": "Allow",
10. 			"Resource": [
11. 				"arn:aws:s3:::DOC-EXAMPLE-DESTINATION-BUCKET",
12. 				"arn:aws:s3:::DOC-EXAMPLE-DESTINATION-BUCKET/*"
13. 			],
14. 			"Principal": {
15. 				"AWS": [
16. 					"arn:aws:iam::111122223333:user/Ana"
17. 				]
18. 			}
19. 		},
20. 		{
21. 			"Sid": "AllowACertainUserToReadObject",
22. 			"Action": [
23. 				"s3:GetObject"
24. 			],
25. 			"Effect": "Allow",
26. 			"Resource": "arn:aws:s3:::DOC-EXAMPLE-DESTINATION-BUCKET/DOC-EXAMPLE-DESTINATION-BUCKET-INVENTORY/*",
27. 			"Principal": {
28. 				"AWS": [
29. 					"arn:aws:iam::111122223333:user/Ana"
30. 				]
31. 			}
32. 		},
33. 		{
34. 			"Sid": "DenyAllTheOtherUsersToReadObject",
35. 			"Action": [
36. 				"s3:GetObject"
37. 			],
38. 			"Effect": "Deny",
39. 			"Resource": "arn:aws:s3:::DOC-EXAMPLE-DESTINATION-BUCKET/DOC-EXAMPLE-DESTINATION-BUCKET-INVENTORY/*",
40. 			"Principal": {
41. 				"AWS": "*"
42. 			},
43. 			"Condition": {
44. 				"ArnNotEquals": {
45. 					"aws:PrincipalArn": "arn:aws:iam::111122223333:user/Ana"
46. 				}
47. 			}
48. 		}
49. 	]
50. }
```

## Granting permissions for Amazon S3 Storage Lens<a name="example-bucket-policies-lens"></a>

Amazon S3 Storage Lens aggregates your usage and activity metrics and displays the information in the account snapshot on the Amazon S3 console home \(**Buckets**\) page, interactive dashboards, or through a metrics export that you can download in CSV or Parquet format\. You can use the dashboard to visualize insights and trends, flag outliers, and receive recommendations for optimizing storage costs and applying data protection best practices\. You can use S3 Storage Lens through the AWS Management Console, AWS CLI, AWS SDKs, or REST API\.

S3 Storage Lens can aggregate your storage usage to metrics exports in an Amazon S3 bucket for further analysis\. The bucket where S3 Storage Lens places its metrics exports is known as the *destination bucket*\. When setting up your S3 Storage Lens metrics export, you must have a bucket policy for the destination bucket\. For more information, see [Assessing your storage activity and usage with Amazon S3 Storage Lens](storage_lens.md)\.

The following example bucket policy grants Amazon S3 permission to write objects \(`PUT` requests\) to a destination bucket\. You use a bucket policy like this on the destination bucket when setting up an S3 Storage Lens metrics export\.

```
 1. {
 2.     "Version": "2012-10-17",
 3.     "Statement": [
 4.         {
 5.             "Sid": "S3StorageLensExamplePolicy",
 6.             "Effect": "Allow",
 7.             "Principal": {
 8.                 "Service": "storage-lens.s3.amazonaws.com"
 9.             },
10.             "Action": "s3:PutObject",
11.             "Resource": [
12.                 "arn:aws:s3:::destination-bucket/destination-prefix/StorageLens/111122223333/*"
13.             ],
14.             "Condition": {
15.                 "StringEquals": {
16.                     "s3:x-amz-acl": "bucket-owner-full-control",
17.                     "aws:SourceAccount": "111122223333",
18.                     "aws:SourceArn": "arn:aws:s3:AWS Region:111122223333:storage-lens/storage-lens-dashboard-configuration-id"
19.                 }
20.             }
21.         }
22.     ]
23. }
```

When setting up an S3 Storage Lens organization\-level metrics export, use the following modification to the previous bucket policy's `Resource` statement\.

```
1. "Resource": "arn:aws:s3:::destination-bucket/destination-prefix/StorageLens/your-organization-id/*",
```