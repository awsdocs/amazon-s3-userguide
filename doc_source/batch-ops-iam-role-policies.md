# Granting permissions for Amazon S3 Batch Operations<a name="batch-ops-iam-role-policies"></a>

Before creating and running S3 Batch Operations jobs, you must grant required permissions\. To create an Amazon S3 Batch Operations job, the `s3:CreateJob` user permission is required\. The same entity that creates the job must also have the `iam:PassRole` permission to pass the AWS Identity and Access Management \(IAM\) role that is specified for the job to Batch Operations\.

For general information about specifying IAM resources, see [IAM JSON policy, Resource elements](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_resource.html) in the *IAM User Guide*\. The following sections provide information about creating an IAM role and attaching policies\.

**Topics**
+ [Creating an S3 Batch Operations IAM role](#batch-ops-iam-role-policies-create)
+ [Attaching permissions policies](#batch-ops-iam-role-policies-perm)

## Creating an S3 Batch Operations IAM role<a name="batch-ops-iam-role-policies-create"></a>

Amazon S3 must have permissions to perform S3 Batch Operations on your behalf\. You grant these permissions through an AWS Identity and Access Management \(IAM\) role\. This section provides examples of the trust and permissions policies you use when creating an IAM role\. For more information, see [IAM roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) in the *IAM User Guide*\. For examples, see [Controlling permissions for S3 Batch Operations using job tags](batch-ops-job-tags-examples.md) and [Copying objects using S3 Batch Operations](batch-ops-examples-copy.md)\.

In your IAM policies, you can also use condition keys to filter access permissions for S3 Batch Operations jobs\. For more information and a complete list of Amazon S3 specific condition keys, see [Actions, resources, and condition keys for Amazon S3](list_amazons3.md)\.

The following video shows how to set up IAM permissions for Batch Operations jobs using the AWS Management Console\.

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/GrxlP39ye20//0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/GrxlP39ye20/)

### Trust policy<a name="batch-ops-iam-role-policies-trust"></a>

To allow the S3 Batch Operations service principal to assume the IAM role, attach the following trust policy to the role\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Principal":{
            "Service":"batchoperations.s3.amazonaws.com"
         },
         "Action":"sts:AssumeRole"
      }
   ]
}
```

## Attaching permissions policies<a name="batch-ops-iam-role-policies-perm"></a>

Depending on the type of operations, you can attach one of the following policies\.

Before you configure permissions, note the following:
+ Regardless of the operation, Amazon S3 needs permissions to read your manifest object from your S3 bucket and optionally write a report to your bucket\. Therefore, all of the following policies include these permissions\.
+ For Amazon S3 inventory report manifests, S3 Batch Operations requires permission to read the manifest\.json object and all associated CSV data files\.
+ Version\-specific permissions such as `s3:GetObjectVersion` are only required when you are specifying the version ID of the objects\.
+ If you are running S3 Batch Operations on encrypted objects, the IAM role must also have access to the AWS KMS keys used to encrypt them\.

### Copy objects: PutObject<a name="batch-ops-put-copy-object-policy"></a>

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "s3:PutObject",
                "s3:PutObjectAcl",
                "s3:PutObjectTagging"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:s3:::{{DestinationBucket}}/*"
        },
        {
            "Action": [
                "s3:GetObject",
                "s3:GetObjectAcl",
                "s3:GetObjectTagging",
                "s3:ListBucket"
            ],
            "Effect": "Allow",
            "Resource": [
                "arn:aws:s3:::{{SourceBucket}}",
                "arn:aws:s3:::{{SourceBucket}}/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:GetObjectVersion"
            ],
            "Resource": [
                "arn:aws:s3:::{{ManifestBucket}}/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject"
            ],
            "Resource": [
                "arn:aws:s3:::{{ReportBucket}}/*"
            ]
        }
    ]
}
```

### Replace object tagging: PutObjectTagging<a name="batch-ops-put-object-tagging-policy"></a>

```
{
  "Version":"2012-10-17",
  "Statement":[
    {
      "Effect":"Allow",
      "Action":[
        "s3:PutObjectTagging",
        "s3:PutObjectVersionTagging"
      ],
      "Resource": "arn:aws:s3:::{{TargetResource}}/*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:GetObjectVersion"
      ],
      "Resource": [
        "arn:aws:s3:::{{ManifestBucket}}/*"
      ]
    },
    {
      "Effect":"Allow",
      "Action":[
        "s3:PutObject"
      ],
      "Resource":[
        "arn:aws:s3:::{{ReportBucket}}/*"
      ]
    }
  ]
}
```

### Delete object tagging: DeleteObjectTagging<a name="batch-ops-delete-object-tagging-policy"></a>

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
              "s3:DeleteObjectTagging",
              "s3:DeleteObjectVersionTagging"
            ],
            "Resource": [
                "arn:aws:s3:::{{TargetResource}}/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:GetObjectVersion"
            ],
            "Resource": [
                "arn:aws:s3:::{{ManifestBucket}}/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject"
            ],
            "Resource": [
                "arn:aws:s3:::{{ReportBucket}}/*"
            ]
        }
    ]
}
```

### Replace access control list: PutObjectAcl<a name="batch-ops-put-object-acl-policy"></a>

```
{
  "Version":"2012-10-17",
  "Statement":[
    {
      "Effect":"Allow",
      "Action":[
        "s3:PutObjectAcl",
        "s3:PutObjectVersionAcl"
      ],
      "Resource": "arn:aws:s3:::{{TargetResource}}/*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:GetObjectVersion"
      ],
      "Resource": [
        "arn:aws:s3:::{{ManifestBucket}}/*"
      ]
    },
    {
      "Effect":"Allow",
      "Action":[
        "s3:PutObject"
      ],
      "Resource":[
        "arn:aws:s3:::{{ReportBucket}}/*"
      ]
    }
  ]
}
```

### Restore objects: RestoreObject<a name="batch-ops-initiate-restore-policy"></a>

```
{
  "Version":"2012-10-17",
  "Statement":[
    {
      "Effect":"Allow",
      "Action":[
          "s3:RestoreObject"
      ],
      "Resource": "arn:aws:s3:::{{TargetResource}}/*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:GetObjectVersion"
      ],
      "Resource": [
        "arn:aws:s3:::{{ManifestBucket}}/*"
      ]
    },
    {
      "Effect":"Allow",
      "Action":[
        "s3:PutObject"
      ],
      "Resource":[
        "arn:aws:s3:::{{ReportBucket}}/*"
      ]
    }
  ]
}
```

### Apply Object Lock retention: PutObjectRetention<a name="batch-ops-put-object-lock-policy"></a>

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:GetBucketObjectLockConfiguration",
            "Resource": [
                "arn:aws:s3:::{{TargetResource}}"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObjectRetention",
                "s3:BypassGovernanceRetention"
            ],
            "Resource": [
                "arn:aws:s3:::{{TargetResource}}/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:GetObjectVersion"
            ],
            "Resource": [
                "arn:aws:s3:::{{ManifestBucket}}/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject"
            ],
            "Resource": [
                "arn:aws:s3:::{{ReportBucket}}/*"
            ]
        }
    ]
}
```

### Apply Object Lock legal hold: PutObjectLegalHold<a name="batch-ops-put-object-legal-hold-policy"></a>

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "s3:GetBucketObjectLockConfiguration",
            "Resource": [
                "arn:aws:s3:::{{TargetResource}}"
            ]
        },
        {
            "Effect": "Allow",
            "Action": "s3:PutObjectLegalHold",
            "Resource": [
                "arn:aws:s3:::{{TargetResource}}/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:GetObjectVersion"
            ],
            "Resource": [
                "arn:aws:s3:::{{ManifestBucket}}/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:PutObject"
            ],
            "Resource": [
                "arn:aws:s3:::{{ReportBucket}}/*"
            ]
        }
    ]
}
```