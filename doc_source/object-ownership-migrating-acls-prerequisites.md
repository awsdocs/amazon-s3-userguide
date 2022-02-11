# Prerequisites for disabling ACLs<a name="object-ownership-migrating-acls-prerequisites"></a>

If your bucket ACL grants access outside of your AWS account, before you disable ACLs, you must migrate your bucket ACL permissions to your bucket policy and reset your bucket ACL to the default private ACL\. If you don't migrate these bucket ACLs, your request to apply the bucket owner enforced setting to disable ACLs fails and returns the [InvalidBucketAclWithObjectOwnership](object-ownership-error-responses.md#object-ownership-error-responses-invalid-acl) error code\. We also recommend that you review your object ACL permissions and migrate them to your bucket policy\. For more information about other suggested prerequisites, see [Prerequisites for disabling ACLs](about-object-ownership.md#object-ownership-considerations)\. 

Each of your existing bucket and object ACLs has an equivalent in an IAM policy\. The following bucket policy examples show you how `READ` and `WRITE` permissions for bucket and object ACLs map to IAM permissions\. For more information about how each ACL translates to IAM permissions, see [Mapping of ACL permissions and access policy permissions](acl-overview.md#acl-access-policy-permission-mapping)\.

To review and migrate ACL permissions to bucket policies, see the following topics\.

**Topics**
+ [Bucket policies examples](#migrate-acl-permissions-bucket-policies)
+ [Using the S3 console to review and migrate ACL permissions](#review-migrate-acl-console)
+ [Using the AWS CLI to review and migrate ACL permissions](#review-migrate-acl-cli)
+ [Example walkthroughs](#object-ownership-migrating-acls)

## Bucket policies examples<a name="migrate-acl-permissions-bucket-policies"></a>

These example bucket policies show you how to migrate `READ` and `WRITE` bucket and object ACL permissions for a third\-party AWS account to a bucket policy\. `READ_ACP` and `WRITE_ACP` ACLs are less relevant for policies because they grant ACL\-related permissions \(`s3:GetBucketAcl`, `s3:GetObjectAcl`, `s3:PutBucketAcl`, and `s3:PutObjectAcl`\)\.

**Example – READ ACL for a bucket**  
If your bucket had a READ ACL that grants AWS account 111122223333 permission to list the contents of your bucket, you can write a bucket policy that grants `s3:ListBucket`, `s3:ListBucketVersions`, `s3:ListBucketMultipartUploads` permissions for your bucket\.   

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Permission to list the objects in a bucket",
            "Effect": "Allow",
            "Principal": {
                "AWS": [
                    "arn:aws:iam::111122223333:root"
                ]
            },
            "Action": [
                "s3:ListBucket",
                "s3:ListBucketVersions",
                "s3:ListBucketMultipartUploads"
            ],
            "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET"
        }
    ]
}
```

**Example – READ ACLs for every object in a bucket**  
If every object in your bucket has a READ ACL that grants access to AWS account 111122223333, you can write a bucket policy that grants `s3:GetObject` and `s3:GetObjectVersion` permissions to this account for every object in your bucket\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Read permission for every object in a bucket",
            "Effect": "Allow",
            "Principal": {
                "AWS": [
                    "arn:aws:iam::111122223333:root"
                ]
            },
            "Action": [
                "s3:GetObject",
                "s3:GetObjectVersion"
            ],
            "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*"
        }
    ]
}
```
This example resource element grants access to a specific object\.  

```
"Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/OBJECT-KEY"
```

**Example – Write ACL that grants permissions to write objects to a bucket**  
If your bucket has a write ACL that grants AWS account 111122223333 permission to write objects to your bucket, you can write a bucket policy that grants `s3:PutObject` permission for your bucket\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "Permission to write objects to a bucket",
            "Effect": "Allow",
            "Principal": {
                "AWS": [
                    "arn:aws:iam::111122223333:root"
                ]
            },
            "Action": [
                "s3:PutObject"
            ],
            "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*"
        }
    ]
}
```

## Using the S3 console to review and migrate ACL permissions<a name="review-migrate-acl-console"></a>

**To review ACL permissions**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. To view bucket ACL permissions, in the **Buckets** list, choose the bucket name\.

1. Choose **Permissions**\.

1. Under **Access control list \(ACL\)**, review your bucket ACL permissions\.

1. Choose the **Objects** tab\.

1. In the **Objects** list, choose your object name\.

1. Choose **Permissions**\.

1. Under **Access control list \(ACL\)**, review your object ACL permissions\.

**To migrate ACL permissions and update your bucket ACL**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the bucket name\.

1. On the **Permissions** tab, under **Bucket policy**, choose **Edit**\.

1. In the **Policy** box, add or update your bucket policy\.

   For example bucket policies, see [Bucket policies examples](#migrate-acl-permissions-bucket-policies) and [Example walkthroughs](#object-ownership-migrating-acls)\.

1. Choose **Save changes**\.

1. [Update your bucket ACL](managing-acls.md) to remove ACL grants to other groups or AWS accounts\.

1. [Apply the bucket owner enforced setting](object-ownership-existing-bucket.md) for Object Ownership\.

## Using the AWS CLI to review and migrate ACL permissions<a name="review-migrate-acl-cli"></a>

1. To return the bucket ACL for your bucket, use the [get\-bucket\-acl](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/get-bucket-acl.html) AWS CLI command:

   ```
   aws s3api get-bucket-acl --bucket DOC-EXAMPLE-BUCKET
   ```

   For example, this bucket ACL grants `WRITE` and `READ` access to a third\-party account\. In this ACL, the third\-party account is identified by the [canonical user ID](finding-canonical-user-id.md)\. To apply the bucket owner enforced setting and disable ACLs, you must migrate these permissions for the third\-party account to a bucket policy\. 

   ```
   {
       "Owner": {
           "DisplayName": "DOC-EXAMPLE-ACCOUNT-OWNER",
           "ID": "852b113e7a2f25102679df27bb0ae12b3f85be6BucketOwnerCanonicalUserID"
       },
       "Grants": [
           {
               "Grantee": {
                   "DisplayName": "DOC-EXAMPLE-ACCOUNT-OWNER",
                   "ID": "852b113e7a2f25102679df27bb0ae12b3f85be6BucketOwnerCanonicalUserID",
                   "Type": "CanonicalUser"
               },
               "Permission": "FULL_CONTROL"
           },
           {
               "Grantee": {
                   "DisplayName": "THIRD-PARTY-EXAMPLE-ACCOUNT",
                   "ID": "72806de9d1ae8b171cca9e2494a8d1335dfced4ThirdPartyAccountCanonicalUserID",
                   "Type": "CanonicalUser"
               },
               "Permission": "READ"
           },
           {
               "Grantee": {
                   "DisplayName": "THIRD-PARTY-EXAMPLE-ACCOUNT",
                   "ID": "72806de9d1ae8b171cca9e2494a8d1335dfced4ThirdPartyAccountCanonicalUserID",
                   "Type": "CanonicalUser"
               },
               "Permission": "WRITE"
           }
       ]
   }
   ```

   For other example ACLs, see [Example walkthroughs](#object-ownership-migrating-acls)\.

1. Migrate your bucket ACL permissions to a bucket policy:

   This example bucket policy grants `s3:PutObject` and `s3:ListBucket` permissions for a third\-party account\. In the bucket policy, the third\-party account is identified by the AWS account ID \(*111122223333*\)\.

   ```
   aws s3api put-bucket-policy --bucket DOC-EXAMPLE-BUCKET --policy file://policy.json
   
   policy.json:
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "PolicyForCrossAccountAllowUpload",
               "Effect": "Allow",
               "Principal": {
                   "AWS": [
                       "arn:aws:iam::111122223333:root"
                   ]
               },
               "Action": [
                   "s3:PutObject",
                   "s3:ListBucket"
               ],
               "Resource": [
               	"arn:aws:s3:::DOC-EXAMPLE-BUCKET",
               	"arn:aws:s3:::DOC-EXAMPLE-BUCKET/*"
           }
       ]
   }
   ```

   For more example bucket policies, see [Bucket policies examples](#migrate-acl-permissions-bucket-policies) and [Example walkthroughs](#object-ownership-migrating-acls)\.

1. To return the ACL for a specific object, use the [get\-object\-acl](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/get-object-acl.html) AWS CLI command\.

   ```
   aws s3api get-object-acl --bucket DOC-EXAMPLE-BUCKET --key EXAMPLE-OBJECT-KEY
   ```

1. If required, migrate object ACL permissions to your bucket policy\. 

   This example resource element grants access to a specific object in a bucket policy\.

   ```
   "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/EXAMPLE-OBJECT-KEY"
   ```

1. Reset the ACL for your bucket to the default ACL\.

   ```
   aws s3api put-bucket-acl --bucket DOC-EXAMPLE-BUCKET --acl private
   ```

1. [Apply the bucket owner enforced setting](object-ownership-existing-bucket.md) for Object Ownership\.

## Example walkthroughs<a name="object-ownership-migrating-acls"></a>

The following examples show you how to migrate ACL permissions to bucket policies for specific use cases\.

**Topics**
+ [Grant access to S3 log delivery group for server access logging](#object-ownership-server-access-logs)
+ [Grant public read access to the objects in a bucket](#object-ownership-public-read)
+ [Grant Amazon ElastiCache for Redis access to your S3 bucket](#object-ownership-elasticache-redis)

### Grant access to S3 log delivery group for server access logging<a name="object-ownership-server-access-logs"></a>

If you want to apply the bucket owner enforced setting to disable ACLs for a server access logging target bucket, you must migrate bucket ACL permissions for the S3 log delivery group to the logging service principal \(`logging.s3.amazonaws.com`\) in a bucket policy\. For more information about log delivery permissions, see [Permissions for log delivery](enable-server-access-logging.md#grant-log-delivery-permissions-general)\.

This bucket ACL grants `WRITE` and `READ_ACP` access to the S3 log delivery group:

```
{
    "Owner": {
        "DisplayName": "DOC-EXAMPLE-ACCOUNT-OWNER", 
        "ID": "852b113e7a2f25102679df27bb0ae12b3f85be6BucketOwnerCanonicalUserID"
    }, 
    "Grants": [
        {
            "Grantee": {
                "Type": "CanonicalUser", 
                "DisplayName": "DOC-EXAMPLE-ACCOUNT-OWNER", 
                "ID": "852b113e7a2f25102679df27bb0ae12b3f85be6BucketOwnerCanonicalUserID"
            }, 
            "Permission": "FULL_CONTROL"
        }, 
        {
            "Grantee": {
                "Type": "Group", 
                "URI": "http://acs.amazonaws.com/groups/s3/LogDelivery"
            }, 
            "Permission": "WRITE"
        }, 
        {
            "Grantee": {
                "Type": "Group", 
                "URI": "http://acs.amazonaws.com/groups/s3/LogDelivery"
            }, 
            "Permission": "READ_ACP"
        }
    ]
}
```

**To migrate bucket ACL permissions for the S3 log delivery group to the logging service principal in a bucket policy**

1. Add the following bucket policy to your target bucket, replacing the example values\.

   ```
   aws s3api put-bucket-policy --bucket DOC-EXAMPLE-BUCKET --policy file://policy.json
   
   policy.json:						{
       {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "S3ServerAccessLogsPolicy",
               "Effect": "Allow",
               "Principal": {
                   "Service": "logging.s3.amazonaws.com"
               },
               "Action": [
                   "s3:PutObject"
               ],
               "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/EXAMPLE-LOGGING-PREFIX*",
               "Condition": {
                   "ArnLike": {
                       "aws:SourceArn": "arn:aws:s3:::SOURCE-BUCKET-NAME"
                   },
                   "StringEquals": {
                       "aws:SourceAccount": "SOURCE-AWS-ACCOUNT-ID"
                   }
               }
           }
       ]
   }
   ```

1. Reset the ACL for your target bucket to the default ACL\.

   ```
   aws s3api put-bucket-acl --bucket DOC-EXAMPLE-BUCKET --acl private
   ```

1. [Apply the bucket owner enforced setting](object-ownership-existing-bucket.md) for Object Ownership to your target bucket\.

### Grant public read access to the objects in a bucket<a name="object-ownership-public-read"></a>

If your object ACLs grant public read access to all of the objects in your bucket, you can migrate these ACL permissions to a bucket policy\.

This object ACL grants public read access to an object in a bucket:

```
{
    "Owner": {
        "DisplayName": "DOC-EXAMPLE-ACCOUNT-OWNER",
        "ID": "852b113e7a2f25102679df27bb0ae12b3f85be6BucketOwnerCanonicalUserID"
    },
    "Grants": [
        {
            "Grantee": {
                "DisplayName": "DOC-EXAMPLE-ACCOUNT-OWNER",
                "ID": "852b113e7a2f25102679df27bb0ae12b3f85be6BucketOwnerCanonicalUserID",
                "Type": "CanonicalUser"
            },
            "Permission": "FULL_CONTROL"
        },
        {
            "Grantee": {
                "Type": "Group",
                "URI": "http://acs.amazonaws.com/groups/global/AllUsers"
            },
            "Permission": "READ"
        }
    ]
}
```

**To migrate public read ACL permissions to a bucket policy**

1. To grant public read access to all of the objects in your bucket, add the following bucket policy, replacing the example values\.

   ```
   aws s3api put-bucket-policy --bucket DOC-EXAMPLE-BUCKET --policy file://policy.json
   
   policy.json:
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Sid": "PublicReadGetObject",
               "Effect": "Allow",
               "Principal": "*",
               "Action": [
                   "s3:GetObject"
               ],
               "Resource": [
                   "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*"
               ]
           }
       ]
   }
   ```

   To grant public access to a specific object in a bucket policy, use the following format for the `Resource` element\. 

   ```
   "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/OBJECT-KEY"
   ```

   To grant public access to all of the objects with a specific prefix, use the following format for the `Resource` element\. 

   ```
   "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/PREFIX/*"
   ```

1. [Apply the bucket owner enforced setting](object-ownership-existing-bucket.md) for Object Ownership\.

### Grant Amazon ElastiCache for Redis access to your S3 bucket<a name="object-ownership-elasticache-redis"></a>

You can [export your ElastiCache for Redis backup](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/backups-exporting.html) to an S3 bucket, which gives you access to the backup from outside ElastiCache\. To export your backup to an S3 bucket, you must grant ElastiCache permissions to copy a snapshot to the bucket\. If you've granted permissions to ElastiCache in a bucket ACL, you must migrate these permissions to a bucket policy before you apply the bucket owner enforced setting to disable ACLs\. For more information, see [Grant ElastiCache access to your Amazon S3 bucket](https://docs.aws.amazon.com/AmazonElastiCache/latest/red-ug/backups-exporting.html#backups-exporting-grant-access) in the *Amazon ElastiCache User Guide*\.

The following example shows the bucket ACL permissions that grant permissions to ElastiCache\. 

```
{
    "Owner": {
        "DisplayName": "DOC-EXAMPLE-ACCOUNT-OWNER",
        "ID": "852b113e7a2f25102679df27bb0ae12b3f85be6BucketOwnerCanonicalUserID"
    },
    "Grants": [
        {
            "Grantee": {
                "DisplayName": "DOC-EXAMPLE-ACCOUNT-OWNER",
                "ID": "852b113e7a2f25102679df27bb0ae12b3f85be6BucketOwnerCanonicalUserID",
                "Type": "CanonicalUser"
            },
            "Permission": "FULL_CONTROL"
        },
        {
            "Grantee": {
                "DisplayName": "aws-scs-s3-readonly",
                "ID": "540804c33a284a299d2547575ce1010f2312ef3da9b3a053c8bc45bf233e4353",
                "Type": "CanonicalUser"
            },
            "Permission": "READ"
        },
        {
            "Grantee": {
                "DisplayName": "aws-scs-s3-readonly",
                "ID": "540804c33a284a299d2547575ce1010f2312ef3da9b3a053c8bc45bf233e4353",
                "Type": "CanonicalUser"
            },
            "Permission": "WRITE"
        },
        {
            "Grantee": {
                "DisplayName": "aws-scs-s3-readonly",
                "ID": "540804c33a284a299d2547575ce1010f2312ef3da9b3a053c8bc45bf233e4353",
                "Type": "CanonicalUser"
            },
            "Permission": "READ_ACP"
        }
    ]
}
```

**To migrate bucket ACL permissions for ElastiCache for Redis to a bucket policy**

1. Add the following bucket policy to your bucket, replacing the example values\.

   ```
   aws s3api put-bucket-policy --bucket DOC-EXAMPLE-BUCKET --policy file://policy.json
   
   policy.json:
   "Id": "Policy15397346",
       "Statement": [
           {
               "Sid": "Stmt15399483",
               "Effect": "Allow",
               "Principal": {
                   "Service": "Region.elasticache-snapshot.amazonaws.com"
               },
               "Action": [
                   "s3:PutObject",
                   "s3:GetObject",
                   "s3:ListBucket",
                   "s3:GetBucketAcl",
                   "s3:ListMultipartUploadParts",
                   "s3:ListBucketMultipartUploads"
               ],
               "Resource": [
                   "arn:aws:s3:::DOC-EXAMPLE-BUCKET",
                   "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*"
               ]
           }
       ]
   }
   ```

1. Reset the ACL for your bucket to the default ACL:

   ```
   aws s3api put-bucket-acl --bucket DOC-EXAMPLE-BUCKET --acl private
   ```

1. [Apply the bucket owner enforced setting](object-ownership-existing-bucket.md) for Object Ownership\.