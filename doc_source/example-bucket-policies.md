# Bucket policy examples<a name="example-bucket-policies"></a>

With Amazon S3 bucket policies, you can secure access to objects in your buckets, so that only users with the appropriate permissions can access them\. You can even prevent authenticated users without the appropriate permissions from accessing your Amazon S3 resources\.

This section presents examples of typical use cases for bucket policies\. These sample policies use `DOC-EXAMPLE-BUCKET` as the resource value\. To test these policies, replace the `user input placeholders` with your own information \(such as your bucket name\)\. 

To grant or deny permissions to a set of objects, you can use wildcard characters \(`*`\) in Amazon Resource Names \(ARNs\) and other values\. For example, you can control access to groups of objects that begin with a common [prefix](https://docs.aws.amazon.com/general/latest/gr/glos-chap.html#keyprefix) or end with a given extension, such as `.html`\. 

For information about bucket policies, see [Using bucket policies](bucket-policies.md)\. For more information about AWS Identity and Access Management \(IAM\) policy language, see [Policies and Permissions in Amazon S3](access-policy-language-overview.md)\.

**Note**  
When testing permissions by using the Amazon S3 console, you must grant additional permissions that the console requires—`s3:ListAllMyBuckets`, `s3:GetBucketLocation`, and `s3:ListBucket`\. For an example walkthrough that grants permissions to users and tests those permissions by using the console, see [Controlling access to a bucket with user policies](walkthrough1.md)\.

**Topics**
+ [Requiring encryption](#example-bucket-policies-encryption)
+ [Managing buckets using canned ACLs](#example-bucket-policies-public-access)
+ [Managing object access with object tagging](#example-bucket-policies-object-tags)
+ [Managing object access by using global condition keys](#example-bucket-policies-global-condition-keys)
+ [Managing access based on specific IP addresses](#example-bucket-policies-IP)
+ [Managing access based on HTTP or HTTPS requests](#example-bucket-policies-HTTP-HTTPS)
+ [Managing user access to specific folders](#example-bucket-policies-folders)
+ [Managing access for access logs](#example-bucket-policies-access-logs)
+ [Managing access to an Amazon CloudFront OAI](#example-bucket-policies-cloudfront)
+ [Managing access for Amazon S3 Storage Lens](#example-bucket-policies-lens)
+ [Managing permissions for S3 Inventory, S3 analytics, and S3 Inventory reports](#example-bucket-policies-s3-inventory)
+ [Requiring MFA](#example-bucket-policies-MFA)

## Requiring encryption<a name="example-bucket-policies-encryption"></a>

### Require SSE\-KMS for all objects written to a bucket<a name="example-bucket-policies-encryption-1"></a>

The following example policy requires every object that is written to the bucket to be encrypted with server\-side encryption using AWS Key Management Service \(AWS KMS\) keys \(SSE\-KMS\)\. If the object isn't encrypted with SSE\-KMS, the request will be denied\.

```
{
"Version": "2012-10-17",
"Id": "PutObjPolicy",
"Statement": [{
  "Sid": "DenyObjectsThatAreNotSSEKMS",
  "Principal": "*",
  "Effect": "Deny",
  "Action": "s3:PutObject",
  "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*",
  "Condition": {
    "Null": {
      "s3:x-amz-server-side-encryption-aws-kms-key-id": "true"
    }
  }
}]
}
```

### Require SSE\-KMS with a specific AWS KMS key for all objects written to a bucket<a name="example-bucket-policies-encryption-2"></a>

The following example policy denies any objects from being written to the bucket if they aren’t encrypted with SSE\-KMS by using a specific KMS key ID\. Even if the objects are encrypted with SSE\-KMS by using a per\-request header or bucket default encryption, the objects cannot be written to the bucket if they haven't been encrypted with the specified KMS key\. Make sure to replace the KMS key ARN that's used in this example with your own KMS key ARN\.

```
{
"Version": "2012-10-17",
"Id": "PutObjPolicy",
"Statement": [{
  "Sid": "DenyObjectsThatAreNotSSEKMSWithSpecificKey",
  "Principal": "*",
  "Effect": "Deny",
  "Action": "s3:PutObject",
  "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*",
  "Condition": {
    "ArnNotEqualsIfExists": {
      "s3:x-amz-server-side-encryption-aws-kms-key-id": "arn:aws:kms:us-east-2:111122223333:key/01234567-89ab-cdef-0123-456789abcdef"
    }
  }
}]
}
```

## Managing buckets using canned ACLs<a name="example-bucket-policies-public-access"></a>

### Granting permissions to multiple accounts to upload objects or set object ACLs for public access<a name="example-bucket-policies-acl-1"></a>

The following example policy grants the `s3:PutObject` and `s3:PutObjectAcl` permissions to multiple AWS accounts and requires that any requests for these operations must include the `public-read` canned access control list \(ACL\)\. For more information, see [Amazon S3 actions](using-with-s3-actions.md) and [Amazon S3 condition key examples](amazon-s3-policy-keys.md)\.

**Warning**  
The `public-read` canned ACL allows anyone in the world to view the objects in your bucket\. Use caution when granting anonymous access to your Amazon S3 bucket or disabling block public access settings\. When you grant anonymous access, anyone in the world can access your bucket\. We recommend that you never grant anonymous access to your Amazon S3 bucket unless you specifically need to, such as with [static website hosting](WebsiteHosting.md)\. If you want to enable block public access settings for static website hosting, see [Tutorial: Configuring a static website on Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/HostingWebsiteOnS3Setup.html)\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AddPublicReadCannedAcl",
            "Effect": "Allow",
            "Principal": {
                "AWS": [
                    "arn:aws:iam::111122223333:root",
                    "arn:aws:iam::444455556666:root"
                ]
            },
            "Action": [
                "s3:PutObject",
                "s3:PutObjectAcl"
            ],
            "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*",
            "Condition": {
                "StringEquals": {
                    "s3:x-amz-acl": [
                        "public-read"
                    ]
                }
            }
        }
    ]
}
```

### Grant cross\-account permissions to upload objects while ensuring that the bucket owner has full control<a name="example-bucket-policies-acl-2"></a>

The following example shows how to allow another AWS account to upload objects to your bucket while ensuring that you have full control of the uploaded objects\. This policy grants a specific AWS account \(*`111122223333`*\) the ability to upload objects only if that account includes the `bucket-owner-full-control` canned ACL on upload\. The `StringEquals` condition in the policy specifies the `s3:x-amz-acl` condition key to express the canned ACL requirement\. For more information, see [Amazon S3 condition key examples](amazon-s3-policy-keys.md)\. 

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

## Managing object access with object tagging<a name="example-bucket-policies-object-tags"></a>

### Allow a user to read only objects that have a specific tag key and value<a name="example-bucket-policies-tagging-1"></a>

The following permissions policy limits a user to only reading objects that have the `environment: production` tag key and value\. This policy uses the `s3:ExistingObjectTag` condition key to specify the tag key and value\.

```
{
  "Version": "2012-10-17",
  "Statement": [
     {"Principal":{"AWS":[
            "arn:aws:iam::111122223333:role/JohnDoe"
      "Effect":     "Allow",
      "Action":     ["s3:GetObject","s3:GetObjectVersion"],
      "Resource":    "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*",
                                      ]
                        }
      "Condition": {  "StringEquals": {"s3:ExistingObjectTag/environment": "production" } }
          }
      ]
}
```

### Restrict which object tag keys that users can add<a name="example-bucket-policies-tagging-2"></a>

The following example policy grants a user permission to perform the `s3:PutObjectTagging` action, which allows a user to add tags to an existing object\. The condition uses the `s3:RequestObjectTagKeys` condition key to specify the allowed tag keys, such as `Owner` or `CreationDate`\. For more information, see [Creating a condition that tests multiple key values](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_multi-value-conditions.html) in the *IAM User Guide*\.

The policy ensures that every tag key specified in the request is an authorized tag key\. The `ForAnyValue` qualifier in the condition ensures that at least one of the specified keys must be present in the request\.

```
{
   "Version": "2012-10-17",
  "Statement": [
    {"Principal":{"AWS":[
            "arn:aws:iam::111122223333:role/JohnDoe"
         ]
       },
 "Effect": "Allow",
      "Action": [
        "s3:PutObjectTagging"
      ],
      "Resource": [
        "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*"
      ],
      "Condition": {"ForAnyValue:StringEquals": {"s3:RequestObjectTagKeys": [
            "Owner",
            "CreationDate"
          ]
        }
      }
    }
  ]
}
```

### Require a specific tag key and value when allowing users to add object tags<a name="example-bucket-policies-tagging-3"></a>

The following example policy grants a user permission to perform the `s3:PutObjectTagging` action, which allows a user to add tags to an existing object\. The condition requires the user to include a specific tag key \(such as `Project`\) with the value set to `X`\.

```
{
   "Version": "2012-10-17",
  "Statement": [
    {"Principal":{"AWS":[
       "arn:aws:iam::111122223333:user/JohnDoe"
         ]
       },
      "Effect": "Allow",
      "Action": [
        "s3:PutObjectTagging"
      ],
      "Resource": [
        "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*"
      ],
      "Condition": {"StringEquals": {"s3:RequestObjectTag/Project": "X"
        }
      }
    }
  ]
}
```

### Allow a user to only add objects with a specific object tag key and value<a name="example-bucket-policies-tagging-4"></a>

The following example policy grants a user permission to perform the `s3:PutObject` action so that they can add objects to a bucket\. However, the `Condition` statement restricts the tag keys and values that are allowed on the uploaded objects\. In this example, the user can only add objects that have the specific tag key \(`Department`\) with the value set to `Finance` to the bucket\.

```
{
    "Version": "2012-10-17",
    "Statement": [{
        "Principal":{
            "AWS":[
                 "arn:aws:iam::111122223333:user/JohnDoe"
         ]
        },
        "Effect": "Allow",
        "Action": [
            "s3:PutObject"
        ],
        "Resource": [
            "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*"
        ],
        "Condition": {
            "StringEquals": {
                "s3:RequestObjectTag/Department": "Finance"
            }
        }
    }]
}
```

## Managing object access by using global condition keys<a name="example-bucket-policies-global-condition-keys"></a>

[Global condition keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html) are condition context keys with an `aws` prefix\. AWS services can support global condition keys or service\-specific keys that include the service prefix\. You can use the `Condition` element of a JSON policy to compare the keys in a request with the key values that you specify in your policy\.

### Restrict access to only Amazon S3 server access log deliveries<a name="example-bucket-policies-global-condition-keys-1"></a>

In the following example bucket policy, the [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourcearn](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-sourcearn) global condition key is used to compare the [Amazon Resource Name \(ARN\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_identifiers.html#identifiers-arns) of the resource, making a service\-to\-service request with the ARN that is specified in the policy\. The `aws:SourceArn` global condition key is used to prevent the Amazon S3 service from being used as a [confused deputy](https://docs.aws.amazon.com/IAM/latest/UserGuide/confused-deputy.html) during transactions between services\. Only the Amazon S3 service is allowed to add objects to the Amazon S3 bucket\.

This example bucket policy grants `s3:PutObject` permissions to only the logging service principal \(`logging.s3.amazonaws.com`\)\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowPutObjectS3ServerAccessLogsPolicy",
            "Principal": {
                "Service": "logging.s3.amazonaws.com"
            },
            "Effect": "Allow",
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET-logs/*",
            "Condition": {
                "StringEquals": {
                    "aws:SourceAccount": "111111111111"
                },
                "ArnLike": {
                    "aws:SourceArn": "arn:aws:s3:::EXAMPLE-SOURCE-BUCKET"
                }
            }
        },
        {
            "Sid": "RestrictToS3ServerAccessLogs",
            "Effect": "Deny",
            "Principal": "*",
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET-logs/*",
            "Condition": {
                "ForAllValues:StringNotEquals": {
                    "aws:PrincipalServiceNamesList": "logging.s3.amazonaws.com"
                }
            }
        }
    ]
}
```

### Allow access to only your organization<a name="example-bucket-policies-global-condition-keys-2"></a>

If you want to require all [IAM principals](https://docs.aws.amazon.com/IAM/latest/UserGuide/intro-structure.html#intro-structure-principal) accessing a resource to be from an AWS account in your organization \(including the AWS Organizations management account\), you can use the `aws:PrincipalOrgID` global condition key\.

To grant or restrict this type of access, define the `aws:PrincipalOrgID` condition and set the value to your [organization ID](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_org_details.html) in the bucket policy\. The organization ID is used to control access to the bucket\. When you use the `aws:PrincipalOrgID` condition, the permissions from the bucket policy are also applied to all new accounts that are added to the organization\.

Here’s an example of a resource\-based bucket policy that you can use to grant specific IAM principals in your organization direct access to your bucket\. By adding the `aws:PrincipalOrgID` global condition key to your bucket policy, the principal account is now required to be in your organization to obtain access to the resource\. Even if you accidentally specify an incorrect account when granting access, the [aws:PrincipalOrgID global condition key](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-principalorgid) acts as an additional safeguard\. When this global key is used in a policy, it prevents all principals from outside of the specified organization from accessing the S3 bucket\. Only principals from accounts in the listed organization are able to obtain access to the resource\.

```
{
    "Version": "2012-10-17",
    "Statement": [{
        "Sid": "AllowGetObject",
        "Principal": {
            "AWS": "*"
        },
        "Effect": "Allow",
        "Action": "s3:GetObject",
        "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*",
        "Condition": {
            "StringEquals": {
                "aws:PrincipalOrgID": ["o-aa111bb222"]
            }
        }
    }]
}
```

## Managing access based on specific IP addresses<a name="example-bucket-policies-IP"></a>

### Restrict access to specific IP addresses<a name="example-bucket-policies-IP-1"></a>

The following example denies all users from performing any Amazon S3 operations on objects in the specified buckets unless the request originates from the specified range of IP addresses\. 

This policy's `Condition` statement identifies *`192.0.2.0/24`* as the range of allowed Internet Protocol version 4 \(IPv4\) IP addresses\. 

The `Condition` block uses the `NotIpAddress` condition and the `aws:SourceIp` condition key, which is an AWS wide condition key\. The `aws:SourceIp` condition key can only be used for public IP address ranges\. For more information about these condition keys, see [Amazon S3 condition key examples](amazon-s3-policy-keys.md)\. The `aws:SourceIp` IPv4 values use standard CIDR notation\. For more information, see [IAM JSON Policy Elements Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#Conditions_IPAddress) in the *IAM User Guide*\. 

**Warning**  
Before using this policy, replace the *`192.0.2.0/24`* IP address range in this example with an appropriate value for your use case\. Otherwise, you will lose the ability to access your bucket\.

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
12.                 "arn:aws:s3:::DOC-EXAMPLE-BUCKET1/*"
13.             ],
14.             "Condition": {
15.                 "NotIpAddress": {
16.                     "aws:SourceIp": "192.0.2.0/24"
17.                 }
18.             }
19.         }
20.     ]
21. }
```

### Allow both IPv4 and IPv6 addresses<a name="example-bucket-policies-IP-2"></a>

When you start using IPv6 addresses, we recommend that you update all of your organization's policies with your IPv6 address ranges in addition to your existing IPv4 ranges\. Doing this will help ensure that the policies continue to work as you make the transition to IPv6\.

The following example bucket policy shows how to mix IPv4 and IPv6 address ranges to cover all of your organization's valid IP addresses\. The example policy allows access to the example IP addresses *`192.0.2.1`* and *`2001:DB8:1234:5678::1`* and denies access to the addresses *`203.0.113.1`* and *`2001:DB8:1234:5678:ABCD::1`*\.

The `aws:SourceIp` condition key can only be used for public IP address ranges\. The IPv6 values for `aws:SourceIp` must be in standard CIDR format\. For IPv6, we support using `::` to represent a range of 0s \(for example, `2001:DB8:1234:5678::/64`\)\. For more information, see [ IP Address Condition Operators](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html#Conditions_IPAddress) in the *IAM User Guide*\.

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
12.                 "arn:aws:s3:::DOC-EXAMPLE-BUCKET1/*"
13.             ],
14.             "Condition": {
15.                 "IpAddress": {
16.                     "aws:SourceIp": [
17.                         "192.0.2.0/24",
18.                         "2001:DB8:1234:5678::/64"
19.                     ]
20.                 },
21.                 "NotIpAddress": {
22.                     "aws:SourceIp": [
23.                         "203.0.113.0/24",
24.                         "2001:DB8:1234:5678:ABCD::/80"
25.                     ]
26.                 }
27.             }
28.         }
29.     ]
30. }
```

## Managing access based on HTTP or HTTPS requests<a name="example-bucket-policies-HTTP-HTTPS"></a>

### Restrict access to only HTTPS requests<a name="example-bucket-policies-use-case-HTTP-HTTPS-1"></a>

If you want to prevent potential attackers from manipulating network traffic, you can use HTTPS \(TLS\) to only allow encrypted connections while restricting HTTP requests from accessing your bucket\. To determine whether the request is HTTP or HTTPS, use the [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-securetransport](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-securetransport) global condition key in your S3 bucket policy\. The `aws:SecureTransport` condition key checks whether a request was sent by using HTTP\.

If a request returns `true`, then the request was sent through HTTP\. If the request returns `false`, then the request was sent through HTTPS\. You can then allow or deny access to your bucket based on the desired request scheme\.

In the following example, the bucket policy explicitly denies access to HTTP requests\. 

```
{
    "Version": "2012-10-17",
    "Statement": [{
        "Sid": "RestrictToTLSRequestsOnly",
        "Action": "s3:*",
        "Effect": "Deny",
        "Resource": [
            "arn:aws:s3:::DOC-EXAMPLE-BUCKET",
            "arn:aws:s3:::DOC-EXAMPLE-BUCKET1/*"
        ],
        "Condition": {
            "Bool": {
                "aws:SecureTransport": "false"
            }
        },
        "Principal": "*"
    }]
}
```

### Restrict access to a specific HTTP referer<a name="example-bucket-policies-HTTP-HTTPS-2"></a>

Suppose that you have a website with the domain name *`www.example.com`* or *`example.com`* with links to photos and videos stored in your bucket named `DOC-EXAMPLE-BUCKET`\. By default, all Amazon S3 resources are private, so only the AWS account that created the resources can access them\. 

To allow read access to these objects from your website, you can add a bucket policy that allows the `s3:GetObject` permission with a condition that the `GET` request must originate from specific webpages\. The following policy restricts requests by using the `StringLike` condition with the `aws:Referer` condition key\.

```
 1. {
 2.   "Version":"2012-10-17",
 3.   "Id":"HTTP referer policy example",
 4.   "Statement":[
 5.     {
 6.       "Sid":"Allow only GET requests originating from www.example.com and example.com.",
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

## Managing user access to specific folders<a name="example-bucket-policies-folders"></a>

### Grant users access to specific folders<a name="example-bucket-policies-folders-1"></a>

Suppose that you're trying to grant users access to a specific folder\. If the IAM user and the S3 bucket belong to the same AWS account, then you can use an IAM policy to grant the user access to a specific bucket folder\. With this approach, you don't need to update your bucket policy to grant access\. You can add the IAM policy to individual IAM users, or you can attach the IAM policy to an IAM role that multiple users can switch to\.

If the IAM identity and the S3 bucket belong to different AWS accounts, then you must grant cross\-account access in both the IAM policy and the bucket policy\. For more information about granting cross\-account access, see [Bucket owner granting cross\-account bucket permissions](https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-walkthroughs-managing-access-example2.html)\.

The following example bucket policy grants `JohnDoe` full console access to only his folder \(`home/JohnDoe/`\)\. By creating a `home` folder and granting the appropriate permissions to your users, you can have multiple users share a single bucket\. This policy consists of three `Allow` statements:
+ `AllowRootAndHomeListingOfCompanyBucket`: Allows the user \(`JohnDoe`\) to list objects at the root level of the `DOC-EXAMPLE-BUCKET` bucket and in the `home` folder\. This statement also allows the user to search on the prefix `home/` by using the console\.
+ `AllowListingOfUserFolder`: Allows the user \(`JohnDoe`\) to list all objects in the `home/JohnDoe/` folder and any subfolders\.
+ `AllowAllS3ActionsInUserFolder`: Allows the user to perform all Amazon S3 actions by granting `Read`, `Write`, and `Delete` permissions\. Permissions are limited to the bucket owner's home folder\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowRootAndHomeListingOfCompanyBucket",
            "Principal": {
                "AWS": [
                    "arn:aws:iam::111122223333:user/JohnDoe"
                ]
            },
            "Effect": "Allow",
            "Action": ["s3:ListBucket"],
            "Resource": ["arn:aws:s3:::DOC-EXAMPLE-BUCKET"],
            "Condition": {
                "StringEquals": {
                    "s3:prefix": ["", "home/", "home/JohnDoe"],
                    "s3:delimiter": ["/"]
                }
            }
        },
        {
            "Sid": "AllowListingOfUserFolder",
            "Principal": {
                "AWS": [
                    "arn:aws:iam::111122223333:user/JohnDoe"
                ]
            },
            "Action": ["s3:ListBucket"],
            "Effect": "Allow",
            "Resource": ["arn:aws:s3:::DOC-EXAMPLE-BUCKET"],
            "Condition": {
                "StringLike": {
                    "s3:prefix": ["home/JohnDoe/*"]
                }
            }
        },
        {
            "Sid": "AllowAllS3ActionsInUserFolder",
            "Effect": "Allow",
            "Principal": {
                "AWS": [
                    "arn:aws:iam::111122223333:user/JohnDoe"
                ]
            },
            "Action": ["s3:*"],
            "Resource": ["arn:aws:s3:::DOC-EXAMPLE-BUCKET/home/JohnDoe/*"]
        }
    ]
}
```

## Managing access for access logs<a name="example-bucket-policies-access-logs"></a>

### Grant access to Application Load Balancer for enabling access logs<a name="example-bucket-policies-access-logs-1"></a>

When you enable access logs for Application Load Balancer, you must specify the name of the S3 bucket where the load balancer will [store the logs](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/enable-access-logging.html#access-log-create-bucket)\. The bucket must have an [attached policy](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/enable-access-logging.html#attach-bucket-policy) that grants Elastic Load Balancing permission to write to the bucket\.

In the following example, the bucket policy grants Elastic Load Balancing \(ELB\) permission to write the access logs to the bucket:

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Principal": {
        "AWS": "arn:aws:iam::elb-account-id:root"
      },
      "Effect": "Allow",
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET1/prefix/AWSLogs/111122223333/*"
    }
  ]
}
```

**Note**  
Make sure to replace `elb-account-id` with the AWS account ID for Elastic Load Balancing for your AWS Region\. For the list of Elastic Load Balancing Regions, see [Attach a policy to your Amazon S3 bucket](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/enable-access-logs.html#attach-bucket-policy) in the *Elastic Load Balancing User Guide*\.

If your AWS Region does not appear in the supported Elastic Load Balancing Regions list, use the following policy, which grants permissions to the specified log delivery service\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
       "Principal": {
         "Service": "logdelivery.elasticloadbalancing.amazonaws.com"
          },
      "Effect": "Allow",
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3::DOC-EXAMPLE-BUCKET/prefix/AWSLogs/111122223333/*"
    }
  ]
}
```

Then, make sure to configure your [Elastic Load Balancing access logs](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/enable-access-logging.html#enable-access-logs) by enabling them\. You can [verify your bucket permissions](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/enable-access-logging.html#verify-bucket-permissions) by creating a test file\.

## Managing access to an Amazon CloudFront OAI<a name="example-bucket-policies-cloudfront"></a>

### Grant permission to an Amazon CloudFront OAI<a name="example-bucket-policies-cloudfront-1"></a>

The following example bucket policy grants a CloudFront origin access identity \(OAI\) permission to get \(read\) all objects in your S3 bucket\. You can use a CloudFront OAI to allow users to access objects in your bucket through CloudFront but not directly through Amazon S3\. For more information, see [Restricting access to Amazon S3 content by using an Origin Access Identity](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html) in the *Amazon CloudFront Developer Guide*\.

The following policy uses the OAI's ID as the policy's `Principal`\. For more information about using S3 bucket policies to grant access to a CloudFront OAI, see [Migrating from origin access identity \(OAI\) to origin access control \(OAC\)](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html#migrate-from-oai-to-oac) in the *Amazon CloudFront Developer Guide*\.

To use this example:
+ Replace `EH1HDMB1FH2TC` with the OAI's ID\. To find the OAI's ID, see the [Origin Access Identity page](https://console.aws.amazon.com/cloudfront/home?region=us-east-1#oai:) on the CloudFront console, or use [https://docs.aws.amazon.com/cloudfront/latest/APIReference/API_ListCloudFrontOriginAccessIdentities.html](https://docs.aws.amazon.com/cloudfront/latest/APIReference/API_ListCloudFrontOriginAccessIdentities.html) in the CloudFront API\.
+ Replace `DOC-EXAMPLE-BUCKET` with the name of your bucket\.

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

## Managing access for Amazon S3 Storage Lens<a name="example-bucket-policies-lens"></a>

### Grant permissions for Amazon S3 Storage Lens<a name="example-bucket-policies-lens-1"></a>

S3 Storage Lens aggregates your metrics and displays the information in the **Account snapshot** section on the Amazon S3 console **Buckets** page\. S3 Storage Lens also provides an interactive dashboard that you can use to visualize insights and trends, flag outliers, and receive recommendations for optimizing storage costs and applying data\-protection best practices\. Your dashboard has drill\-down options to generate insights at the organization, account, bucket, object, or prefix level\. You can also send a once\-daily metrics export in CSV or Parquet format to an S3 bucket\.

S3 Storage Lens can export your aggregated storage usage metrics to an Amazon S3 bucket for further analysis\. The bucket where S3 Storage Lens places its metrics exports is known as the *destination bucket*\. When setting up your S3 Storage Lens metrics export, you must have a bucket policy for the destination bucket\. For more information, see [Assessing your storage activity and usage with Amazon S3 Storage Lens](storage_lens.md)\.

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
18.                     "aws:SourceArn": "arn:aws:s3:region-code:111122223333:storage-lens/storage-lens-dashboard-configuration-id"
19.                 }
20.             }
21.         }
22.     ]
23. }
```

When you're setting up an S3 Storage Lens organization\-level metrics export, use the following modification to the previous bucket policy's `Resource` statement\.

```
1. "Resource": "arn:aws:s3:::destination-bucket/destination-prefix/StorageLens/your-organization-id/*",
```

## Managing permissions for S3 Inventory, S3 analytics, and S3 Inventory reports<a name="example-bucket-policies-s3-inventory"></a>

### Grant permissions for S3 Inventory and S3 analytics<a name="example-bucket-policies-s3-inventory-1"></a>

S3 Inventory creates lists of the objects in a bucket, and S3 analytics Storage Class Analysis export creates output files of the data used in the analysis\. The bucket that the inventory lists the objects for is called the *source bucket*\. The bucket where the inventory file or the analytics export file is written to is called a *destination bucket*\. When setting up an inventory or an analytics export, you must create a bucket policy for the destination bucket\. For more information, see [Amazon S3 Inventory](storage-inventory.md) and [Amazon S3 analytics – Storage Class Analysis](analytics-storage-class.md)\.

The following example bucket policy grants Amazon S3 permission to write objects \(`PUT` requests\) from the account for the source bucket to the destination bucket\. You use a bucket policy like this on the destination bucket when setting up S3 Inventory and S3 analytics export\.

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
12.                 "arn:aws:s3:::DOC-EXAMPLE-DESTINATION-BUCKET/*"
13.             ],
14.             "Condition": {
15.                 "ArnLike": {
16.                     "aws:SourceArn": "arn:aws:s3:::DOC-EXAMPLE-SOURCE-BUCKET"
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

### Restrict access to an S3 Inventory report<a name="example-bucket-policies-s3-inventory-2"></a>

[Amazon S3 Inventory](storage-inventory.md) creates lists of the objects in an S3 bucket and the metadata for each object\. The `s3:PutInventoryConfiguration` permission allows a user to create an inventory report that includes all object metadata fields that are available and to specify the destination bucket to store the inventory\. A user with read access to objects in the destination bucket can access all object metadata fields that are available in the inventory report\. For more information about the metadata fields that are available in S3 Inventory, see [Amazon S3 Inventory list](storage-inventory.md#storage-inventory-contents)\.

To restrict a user from configuring an S3 Inventory report of all object metadata available, remove the `s3:PutInventoryConfiguration` permission from the user\.

To restrict a user from accessing your S3 Inventory report in a destination bucket, add a bucket policy like the following example to the destination bucket\. This example bucket policy denies all the principals except the user `Ana` from accessing the inventory report `DOC-EXAMPLE-DESTINATION-BUCKET-INVENTORY` in the destination bucket `DOC-EXAMPLE-DESTINATION-BUCKET`\.

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

## Requiring MFA<a name="example-bucket-policies-MFA"></a>

Amazon S3 supports MFA\-protected API access, a feature that can enforce multi\-factor authentication \(MFA\) for access to your Amazon S3 resources\. Multi\-factor authentication provides an extra level of security that you can apply to your AWS environment\. MFA is a security feature that requires users to prove physical possession of an MFA device by providing a valid MFA code\. For more information, see [AWS Multi\-Factor Authentication](https://aws.amazon.com/mfa/)\. You can require MFA for any requests to access your Amazon S3 resources\. 

To enforce the MFA requirement, use the `aws:MultiFactorAuthAge` condition key in a bucket policy\. IAM users can access Amazon S3 resources by using temporary credentials issued by the AWS Security Token Service \(AWS STS\)\. You provide the MFA code at the time of the AWS STS request\. 

When Amazon S3 receives a request with multi\-factor authentication, the `aws:MultiFactorAuthAge` condition key provides a numeric value that indicates how long ago \(in seconds\) the temporary credential was created\. If the temporary credential provided in the request was not created by using an MFA device, this key value is null \(absent\)\. In a bucket policy, you can add a condition to check this value, as shown in the following example\. 

This example policy denies any Amazon S3 operation on the *`/taxdocuments`* folder in the `DOC-EXAMPLE-BUCKET` bucket if the request is not authenticated by using MFA\. To learn more about MFA, see [Using Multi\-Factor Authentication \(MFA\) in AWS](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_mfa.html) in the *IAM User Guide*\.

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

The `Null` condition in the `Condition` block evaluates to `true` if the `aws:MultiFactorAuthAge` condition key value is null, indicating that the temporary security credentials in the request were created without an MFA device\. 

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

You can optionally use a numeric condition to limit the duration for which the `aws:MultiFactorAuthAge` key is valid\. The duration that you specify with the `aws:MultiFactorAuthAge` key is independent of the lifetime of the temporary security credential that's used in authenticating the request\. 

For example, the following bucket policy, in addition to requiring MFA authentication, also checks how long ago the temporary session was created\. The policy denies any operation if the `aws:MultiFactorAuthAge` key value indicates that the temporary session was created more than an hour ago \(3,600 seconds\)\. 

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