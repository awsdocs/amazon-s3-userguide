# Amazon S3 condition key examples<a name="amazon-s3-policy-keys"></a>

You can use access policy language to specify conditions when you grant permissions\. You can use the optional `Condition` element, or `Condition` block to specify conditions for when a policy is in effect\. 

For policies that use Amazon S3 condition keys for object and bucket operations, see the following examples\. For more information about condition keys, see [Amazon S3 condition keys](amazon-s3-condition-keys.md)\. For a complete list of Amazon S3 actions, condition keys, and resources that you can specify in policies, see [Actions, resources, and condition keys for Amazon S3](list_amazons3.md)\.

## Examples — Amazon S3 condition keys for object operations<a name="object-keys-in-amazon-s3-policies"></a>

This section provides examples that show you how you can use Amazon S3‐specific condition keys for object operations\. For a complete list of Amazon S3 actions, condition keys, and resources that you can specify in policies, see [Actions, resources, and condition keys for Amazon S3](list_amazons3.md)\.

Several of the example policies show how you can use conditions keys with [PUT Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPUT.html) operations\. PUT Object operations allow access control list \(ACL\)–specific headers that you can use to grant ACL\-based permissions\. Using these keys, the bucket owner can set a condition to require specific access permissions when the user uploads an object\. You can also grant ACL–based permissions with the PutObjectAcl operation\. For more information, see [PutObjectAcl](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectAcl.html) in the *Amazon S3 Amazon Simple Storage Service API Reference*\. For more information about ACLs, see [Access control list \(ACL\) overview](acl-overview.md)\.

**Topics**
+ [Example 1: Granting s3:PutObject permission with a condition requiring the bucket owner to get full control](#grant-putobject-conditionally-1)
+ [Example 2: Granting s3:PutObject permission requiring objects stored using server\-side encryption](#putobject-require-sse-2)
+ [Example 3: Granting s3:PutObject permission to copy objects with a restriction on the copy source](#putobject-limit-copy-source-3)
+ [Example 4: Granting access to a specific version of an object](#getobjectversion-limit-access-to-specific-version-3)
+ [Example 5: Restricting object uploads to objects with a specific storage class](#example-storage-class-condition-key)
+ [Example 6: Granting permissions based on object tags](#example-object-tagging-access-control)
+ [Example 7: Restricting access by the AWS account ID of the bucket owner](#example-object-resource-account)
+ [Example 8: Requiring a minimum TLS version](#example-object-tls-version)

### Example 1: Granting s3:PutObject permission with a condition requiring the bucket owner to get full control<a name="grant-putobject-conditionally-1"></a>

The [PUT Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPUT.html) operation allows access control list \(ACL\)–specific headers that you can use to grant ACL\-based permissions\. Using these keys, the bucket owner can set a condition to require specific access permissions when the user uploads an object\. 

Suppose that Account A owns a bucket, and the account administrator wants to grant Dave, a user in Account B, permissions to upload objects\. By default, objects that Dave uploads are owned by Account B, and Account A has no permissions on these objects\. Because the bucket owner is paying the bills, it wants full permissions on the objects that Dave uploads\. The Account A administrator can do this by granting the `s3:PutObject` permission to Dave, with a condition that the request include ACL\-specific headers that either grant full permission explicitly or use a canned ACL\. For more information, see [PUT Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPUT.html)\.

#### Require the x\-amz\-full\-control header<a name="require-x-amz-full-control"></a>

You can require the `x-amz-full-control` header in the request with full control permission to the bucket owner\. The following bucket policy grants the `s3:PutObject` permission to user Dave with a condition using the `s3:x-amz-grant-full-control` condition key, which requires the request to include the `x-amz-full-control` header\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "statement1",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::AccountB-ID:user/Dave"
      },
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::awsexamplebucket1/*",
      "Condition": {
        "StringEquals": {
          "s3:x-amz-grant-full-control": "id=AccountA-CanonicalUserID"
        }
      }
    }
  ]
}
```

**Note**  
This example is about cross\-account permission\. However, if Dave \(who is getting the permission\) belongs to the AWS account that owns the bucket, this conditional permission is not necessary\. This is because the parent account to which Dave belongs owns objects that the user uploads\.

**Add explicit deny**  
The preceding bucket policy grants conditional permission to user Dave in Account B\. While this policy is in effect, it is possible for Dave to get the same permission without any condition via some other policy\. For example, Dave can belong to a group, and you grant the group `s3:PutObject` permission without any condition\. To avoid such permission loopholes, you can write a stricter access policy by adding explicit deny\. In this example, you explicitly deny the user Dave upload permission if he does not include the necessary headers in the request granting full permissions to the bucket owner\. Explicit deny always supersedes any other permission granted\. The following is the revised access policy example with explicit deny added\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "statement1",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::AccountB-ID:user/AccountBadmin"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::awsexamplebucket1/*",
            "Condition": {
                "StringEquals": {
                    "s3:x-amz-grant-full-control": "id=AccountA-CanonicalUserID"
                }
            }
        },
        {
            "Sid": "statement2",
            "Effect": "Deny",
            "Principal": {
                "AWS": "arn:aws:iam::AccountB-ID:user/AccountBadmin"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::awsexamplebucket1/*",
            "Condition": {
                "StringNotEquals": {
                    "s3:x-amz-grant-full-control": "id=AccountA-CanonicalUserID"
                }
            }
        }
    ]
}
```

**Test the policy with the AWS CLI**  
If you have two AWS accounts, you can test the policy using the AWS Command Line Interface \(AWS CLI\)\. You attach the policy and use Dave's credentials to test the permission using the following AWS CLI `put-object` command\. You provide Dave's credentials by adding the `--profile` parameter\. You grant full control permission to the bucket owner by adding the `--grant-full-control` parameter\. For more information about setting up and using the AWS CLI, see [Developing with Amazon S3 using the AWS CLI](setup-aws-cli.md)\. 

```
aws s3api put-object --bucket examplebucket --key HappyFace.jpg --body c:\HappyFace.jpg --grant-full-control id="AccountA-CanonicalUserID" --profile AccountBUserProfile
```

#### Require the x\-amz\-acl header<a name="require-x-amz-acl-header"></a>

You can require the `x-amz-acl` header with a canned ACL granting full control permission to the bucket owner\. To require the `x-amz-acl` header in the request, you can replace the key\-value pair in the `Condition` block and specify the `s3:x-amz-acl` condition key, as shown in the following example\.

```
"Condition": {
    "StringEquals": {
        "s3:x-amz-acl": "bucket-owner-full-control"
    }
}
```

To test the permission using the AWS CLI, you specify the `--acl` parameter\. The AWS CLI then adds the `x-amz-acl` header when it sends the request\.

```
aws s3api put-object --bucket examplebucket --key HappyFace.jpg --body c:\HappyFace.jpg --acl "bucket-owner-full-control" --profile AccountBadmin
```

### Example 2: Granting s3:PutObject permission requiring objects stored using server\-side encryption<a name="putobject-require-sse-2"></a>

Suppose that Account A owns a bucket\. The account administrator wants to grant Jane, a user in Account A, permission to upload objects with a condition that Jane always request server\-side encryption so that Amazon S3 saves objects encrypted\. The Account A administrator can accomplish using the `s3:x-amz-server-side-encryption` condition key as shown\. The key\-value pair in the `Condition` block specifies the `s3:x-amz-server-side-encryption` key\.

```
"Condition": {
     "StringNotEquals": {
         "s3:x-amz-server-side-encryption": "AES256"
     }
```

When testing the permission using the AWS CLI, you must add the required parameter using the `--server-side-encryption` parameter\.

```
aws s3api put-object --bucket example1bucket --key HappyFace.jpg --body c:\HappyFace.jpg --server-side-encryption "AES256" --profile AccountBadmin
```

### Example 3: Granting s3:PutObject permission to copy objects with a restriction on the copy source<a name="putobject-limit-copy-source-3"></a>

In the PUT Object request, when you specify a source object, it is a copy operation \(see [PUT Object \- Copy](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectCOPY.html)\)\. Accordingly, the bucket owner can grant a user permission to copy objects with restrictions on the source, for example:
+ Allow copying objects only from the `sourcebucket` bucket\.
+ Allow copying objects from the source bucket and only the objects whose key name prefix starts with public/ f \(for example, sourcebucket/public/\*\)\.
+ Allow copying only a specific object from the sourcebucket \(for example, sourcebucket/example\.jpg\)\.

The following bucket policy grants user \(Dave\) `s3:PutObject` permission\. It allows him to copy objects only with a condition that the request include the `s3:x-amz-copy-source` header and the header value specify the `/awsexamplebucket1/public/*` key name prefix\. 

```
{
    "Version": "2012-10-17",
    "Statement": [
       {
            "Sid": "cross-account permission to user in your own account",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::123456789012:user/Dave"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::awsexamplebucket1/*"
        },
        {
            "Sid": "Deny your user permission to upload object if copy source is not /bucket/folder",
            "Effect": "Deny",
            "Principal": {
                "AWS": "arn:aws:iam::123456789012:user/Dave"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::awsexamplebucket1/*",
            "Condition": {
                "StringNotLike": {
                    "s3:x-amz-copy-source": "awsexamplebucket1/public/*"
                }
            }
        }
    ]
}
```

**Test the policy with the AWS CLI**  
You can test the permission using the AWS CLI `copy-object` command\. You specify the source by adding the `--copy-source` parameter; the key name prefix must match the prefix allowed in the policy\. You need to provide the user Dave credentials using the `--profile` parameter\. For more information about setting up the AWS CLI, see [Developing with Amazon S3 using the AWS CLI](setup-aws-cli.md)\.

```
aws s3api copy-object --bucket awsexamplebucket1 --key HappyFace.jpg 
--copy-source examplebucket/public/PublicHappyFace1.jpg --profile AccountADave
```

**Give permission to copy only a specific object**  
The preceding policy uses the `StringNotLike` condition\. To grant permission to copy only a specific object, you must change the condition from `StringNotLike` to `StringNotEquals` and then specify the exact object key as shown\. 

```
"Condition": {
       "StringNotEquals": {
           "s3:x-amz-copy-source": "awsexamplebucket1/public/PublicHappyFace1.jpg"
       }
}
```

### Example 4: Granting access to a specific version of an object<a name="getobjectversion-limit-access-to-specific-version-3"></a>

Suppose that Account A owns a version\-enabled bucket\. The bucket has several versions of the `HappyFace.jpg` object\. The account administrator now wants to grant its user Dave permission to get only a specific version of the object\. The account administrator can accomplish this by granting Dave `s3:GetObjectVersion` permission conditionally as shown below\. The key\-value pair in the `Condition` block specifies the `s3:VersionId` condition key\. In this case, Dave needs to know the exact object version ID to retrieve the object\. 

For more information, see [GetObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html) in the *Amazon Simple Storage Service API Reference*\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "statement1",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::123456789012:user/Dave"
            },
            "Action": "s3:GetObjectVersion",
            "Resource": "arn:aws:s3:::examplebucketversionenabled/HappyFace.jpg"
        },
        {
            "Sid": "statement2",
            "Effect": "Deny",
            "Principal": {
                "AWS": "arn:aws:iam::123456789012:user/Dave"
            },
            "Action": "s3:GetObjectVersion",
            "Resource": "arn:aws:s3:::examplebucketversionenabled/HappyFace.jpg",
            "Condition": {
                "StringNotEquals": {
                    "s3:VersionId": "AaaHbAQitwiL_h47_44lRO2DDfLlBO5e"
                }
            }
        }
    ]
}
```

**Test the policy with the AWS CLI**  
You can test the permissions using the AWS CLI `get-object` command with the `--version-id` parameter identifying the specific object version\. The command retrieves the object and saves it to the `OutputFile.jpg` file\.

```
aws s3api get-object --bucket examplebucketversionenabled --key HappyFace.jpg OutputFile.jpg --version-id AaaHbAQitwiL_h47_44lRO2DDfLlBO5e --profile AccountADave
```

### Example 5: Restricting object uploads to objects with a specific storage class<a name="example-storage-class-condition-key"></a>

Suppose that Account A, represented by account ID 123456789012, owns a bucket\. The account administrator wants to restrict Dave, a user in Account A, to be able to only upload objects to the bucket that are stored with the `STANDARD_IA` storage class\. To restrict object uploads to a specific storage class, the Account A administrator can use the `s3:x-amz-storage-class` condition key,as shown in the following example bucket policy\. 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "statement1",
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:user/Dave"
      },
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET1/*",
      "Condition": {
        "StringEquals": {
          "s3:x-amz-storage-class": [
            "STANDARD_IA"
          ]
        }
      }
    }
  ]
}
```

### Example 6: Granting permissions based on object tags<a name="example-object-tagging-access-control"></a>

For examples on how to use object tagging condition keys with Amazon S3 operations, see [Tagging and access control policies](tagging-and-policies.md)\.

### Example 7: Restricting access by the AWS account ID of the bucket owner<a name="example-object-resource-account"></a>

You can use either the `aws:ResourceAccount` or `s3:ResourceAccount` key to write IAM or virtual private cloud \(VPC\) endpoint policies that restrict user, role, or application access to the Amazon S3 buckets that are owned by a specific AWS account ID\. You can use this condition key to restrict clients within your VPC from accessing buckets that you do not own\.

However, be aware that some AWS services rely on access to AWS managed buckets\. Therefore, using the `aws:ResourceAccount` or `s3:ResourceAccount` key in your IAM policy might also affect access to these resources\.

For more information and examples, see the following resources:
+ [Restrict access to buckets in a specified AWS account](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints-s3.html#vpc-endpoints-policies-s3) in the *AWS PrivateLink Guide*
+ [Restrict access to buckets that Amazon ECR uses](https://docs.aws.amazon.com/AmazonECR/latest/userguide/vpc-endpoints.html#ecr-minimum-s3-perms) in the *Amazon ECR Guide*
+ [Provide required access to Systems Manager for AWS managed Amazon S3 buckets](https://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-agent-minimum-s3-permissions.html) in the *AWS Systems Manager Guide*
+ [Limit access to Amazon S3 buckets owned by specific AWS accounts](http://aws.amazon.com/blogs/storage/limit-access-to-amazon-s3-buckets-owned-by-specific-aws-accounts/) in the *AWS Storage Blog*

### Example 8: Requiring a minimum TLS version<a name="example-object-tls-version"></a>

You can use the s3:TlsVersion condition key to write IAM, Virtual Private Cloud Endpoint \(VPCE\), or bucket policies that restrict user or application access to Amazon S3 buckets based on the TLS version used by the client\. You can use this condition key to write policies that require a minimum TLS version\. 

**Example**  
This example bucket policy *denies* PutObject requests by clients that have a TLS version lower than 1\.2, for example, 1\.1 or 1\.0\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Deny",
            "Principal": "*",
            "Action": "s3:PutObject",
            "Resource": [
                "arn:aws:s3:::DOC-EXAMPLE-BUCKET1",
                "arn:aws:s3:::DOC-EXAMPLE-BUCKET1/*"
            ],
            "Condition": {
                "NumericLessThan": {
                    "s3:TlsVersion": 1.2
                }
            }
        }
    ]
}
```

**Example**  
This example bucket policy *allows* PutObject requests by clients that have a TLS version higher than 1\.1, for example, 1\.2, 1\.3 or higher\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:PutObject",
            "Resource": [
                "arn:aws:s3:::DOC-EXAMPLE-BUCKET1",
                "arn:aws:s3:::DOC-EXAMPLE-BUCKET1/*"
            ],
            "Condition": {
                "NumericGreaterThan": {
                    "s3:TlsVersion": 1.1
                }
            }
        }
    ]
}
```

## Examples — Amazon S3 condition keys for bucket operations<a name="bucket-keys-in-amazon-s3-policies"></a>

This section provides example policies that show you how you can use Amazon S3‐specific condition keys for bucket operations\.

**Topics**
+ [Example 1: Granting a user permission to create a bucket only in a specific Region](#condition-key-bucket-ops-1)
+ [Example 2: Getting a list of objects in a bucket with a specific prefix](#condition-key-bucket-ops-2)
+ [Example 3: Setting the maximum number of keys](#example-numeric-condition-operators)

### Example 1: Granting a user permission to create a bucket only in a specific Region<a name="condition-key-bucket-ops-1"></a>

Suppose that an AWS account administrator wants to grant its user \(Dave\) permission to create a bucket in the South America \(São Paulo\) Region only\. The account administrator can attach the following user policy granting the `s3:CreateBucket` permission with a condition as shown\. The key\-value pair in the `Condition` block specifies the `s3:LocationConstraint` key and the `sa-east-1` Region as its value\.

**Note**  
In this example, the bucket owner is granting permission to one of its users, so either a bucket policy or a user policy can be used\. This example shows a user policy\.

For a list of Amazon S3 Regions, see [Regions and Endpoints](https://docs.aws.amazon.com/general/latest/gr/s3.html) in the *AWS General Reference*\. 

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Sid":"statement1",
         "Effect":"Allow",
         "Action": "s3:CreateBucket",
         "Resource": "arn:aws:s3:::*",
         "Condition": {
             "StringLike": {
                 "s3:LocationConstraint": "sa-east-1"
             }
         }
       }
    ]
}
```

**Add explicit deny**  
The preceding policy restricts the user from creating a bucket in any other Region except `sa-east-1`\. However, some other policy might grant this user permission to create buckets in another Region\. For example, if the user belongs to a group, the group might have a policy attached to it that allows all users in the group permission to create buckets in another Region\. To ensure that the user does not get permission to create buckets in any other Region, you can add an explicit deny statement in the above policy\. 

The `Deny` statement uses the `StringNotLike` condition\. That is, a create bucket request is denied if the location constraint is not `sa-east-1`\. The explicit deny does not allow the user to create a bucket in any other Region, no matter what other permission the user gets\. The below policy includes an explicit deny statement\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Sid":"statement1",
         "Effect":"Allow",
         "Action": "s3:CreateBucket",
         "Resource": "arn:aws:s3:::*",
         "Condition": {
             "StringLike": {
                 "s3:LocationConstraint": "sa-east-1"
             }
         }
       },
      {
         "Sid":"statement2",
         "Effect":"Deny",
         "Action": "s3:CreateBucket",
         "Resource": "arn:aws:s3:::*",
         "Condition": {
             "StringNotLike": {
                 "s3:LocationConstraint": "sa-east-1"
             }
         }
       }
    ]
}
```

**Test the policy with the AWS CLI**  
You can test the policy using the following `create-bucket` AWS CLI command\. This example uses the `bucketconfig.txt` file to specify the location constraint\. Note the Windows file path\. You need to update the bucket name and path as appropriate\. You must provide user credentials using the `--profile` parameter\. For more information about setting up and using the AWS CLI, see [Developing with Amazon S3 using the AWS CLI](setup-aws-cli.md)\.

```
aws s3api create-bucket --bucket examplebucket --profile AccountADave --create-bucket-configuration file://c:/Users/someUser/bucketconfig.txt
```

The `bucketconfig.txt` file specifies the configuration as follows\.

```
{"LocationConstraint": "sa-east-1"}
```

### Example 2: Getting a list of objects in a bucket with a specific prefix<a name="condition-key-bucket-ops-2"></a>

You can use the `s3:prefix` condition key to limit the response of the [GET Bucket \(ListObjects\)](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListObjects.html) API to key names with a specific prefix\. If you are the bucket owner, you can restrict a user to list the contents of a specific prefix in the bucket\. This condition key is useful if objects in the bucket are organized by key name prefixes\. The Amazon S3 console uses key name prefixes to show a folder concept\. Only the console supports the concept of folders; the Amazon S3 API supports only buckets and objects\. For more information about using prefixes and delimiters to filter access permissions, see [Controlling access to a bucket with user policies](walkthrough1.md)\.

For example, if you have two objects with key names `public/object1.jpg` and `public/object2.jpg`, the console shows the objects under the `public` folder\. In the Amazon S3 API, these are objects with prefixes, not objects in folders\. However, in the Amazon S3 API, if you organize your object keys using such prefixes, you can grant `s3:ListBucket` permission with the `s3:prefix` condition that will allow the user to get a list of key names with those specific prefixes\. 

In this example, the bucket owner and the parent account to which the user belongs are the same\. So the bucket owner can use either a bucket policy or a user policy\. For more information about other condition keys that you can use with the GET Bucket \(ListObjects\) API, see [ ListObjects](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListObjects.html)\.

**User policy**  
The following user policy grants the `s3:ListBucket` permission \(see [GET Bucket \(List Objects\)](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGET.html)\) with a condition that requires the user to specify the `prefix` in the request with the value `projects`\. 

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Sid":"statement1",
         "Effect":"Allow",
         "Action": "s3:ListBucket",
         "Resource":"arn:aws:s3:::awsexamplebucket1",
         "Condition" : {
             "StringEquals" : {
                 "s3:prefix": "projects" 
             }
          } 
       },
      {
         "Sid":"statement2",
         "Effect":"Deny",
         "Action": "s3:ListBucket",
         "Resource": "arn:aws:s3:::awsexamplebucket1",
         "Condition" : {
             "StringNotEquals" : {
                 "s3:prefix": "projects" 
             }
          } 
       }         
    ]
}
```

The condition restricts the user to listing object keys with the `projects` prefix\. The added explicit deny denies the user request for listing keys with any other prefix no matter what other permissions the user might have\. For example, it is possible that the user gets permission to list object keys without any restriction, either by updates to the preceding user policy or via a bucket policy\. Because explicit deny always supersedes, the user request to list keys other than the `projects` prefix is denied\. 

**Bucket policy**  
If you add the `Principal` element to the above user policy, identifying the user, you now have a bucket policy as shown\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Sid":"statement1",
         "Effect":"Allow",
         "Principal": {
            "AWS": "arn:aws:iam::123456789012:user/bucket-owner"
         },  
         "Action":  "s3:ListBucket",
         "Resource": "arn:aws:s3:::awsexamplebucket1",
         "Condition" : {
             "StringEquals" : {
                 "s3:prefix": "projects" 
             }
          } 
       },
      {
         "Sid":"statement2",
         "Effect":"Deny",
         "Principal": {
            "AWS": "arn:aws:iam::123456789012:user/bucket-owner"
         },  
         "Action": "s3:ListBucket",
         "Resource": "arn:aws:s3:::awsexamplebucket1",
         "Condition" : {
             "StringNotEquals" : {
                 "s3:prefix": "projects"  
             }
          } 
       }         
    ]
}
```

**Test the policy with the AWS CLI**  
You can test the policy using the following `list-object` AWS CLI command\. In the command, you provide user credentials using the `--profile` parameter\. For more information about setting up and using the AWS CLI, see [Developing with Amazon S3 using the AWS CLI](setup-aws-cli.md)\.

```
aws s3api list-objects --bucket awsexamplebucket1 --prefix examplefolder --profile AccountADave
```

If the bucket is version\-enabled, to list the objects in the bucket, you must grant the `s3:ListBucketVersions` permission in the preceding policy, instead of `s3:ListBucket` permission\. This permission also supports the `s3:prefix` condition key\. 

### Example 3: Setting the maximum number of keys<a name="example-numeric-condition-operators"></a>

You can use the `s3:max-keys` condition key to set the maximum number of keys that requester can return in a [GET Bucket \(ListObjects\)](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListObjects.html) or [ListObjectVersions](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListObjectVersions.html) request\. By default, the API returns up to 1,000 keys\. For a list of numeric condition operators that you can use with `s3:max-keys` and accompanying examples, see [Numeric Condition Operators](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition_operators.html#Conditions_Numeric) in the *IAM User Guide*\.