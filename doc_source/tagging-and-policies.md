# Tagging and access control policies<a name="tagging-and-policies"></a>

You can also use permissions policies \(bucket and user policies\) to manage permissions related to object tagging\. For policy actions see the following topics: 
+  [Example — Object operations](using-with-s3-actions.md#using-with-s3-actions-related-to-objects) 
+  [Example — Bucket operations](using-with-s3-actions.md#using-with-s3-actions-related-to-buckets)

Object tags enable fine\-grained access control for managing permissions\. You can grant conditional permissions based on object tags\. Amazon S3 supports the following condition keys that you can use to grant conditional permissions based on object tags:
+ `s3:ExistingObjectTag/<tag-key>` – Use this condition key to verify that an existing object tag has the specific tag key and value\. 
**Note**  
When granting permissions for the `PUT Object` and `DELETE Object` operations, this condition key is not supported\. That is, you cannot create a policy to grant or deny a user permissions to delete or overwrite an object based on its existing tags\. 
+ `s3:RequestObjectTagKeys` – Use this condition key to restrict the tag keys that you want to allow on objects\. This is useful when adding tags to objects using the PutObjectTagging and PutObject, and POST object requests\.
+ `s3:RequestObjectTag/<tag-key>` – Use this condition key to restrict the tag keys and values that you want to allow on objects\. This is useful when adding tags to objects using the PutObjectTagging and PutObject, and POST Bucket requests\.

For a complete list of Amazon S3 service\-specific condition keys, see [Amazon S3 condition key examples](amazon-s3-policy-keys.md)\. The following permissions policies illustrate how object tagging enables fine grained access permissions management\.

**Example 1: Allow a user to read only the objects that have a specific tag and key value**  
The following permissions policy limits a user to only reading objects that have the `environment: production` tag key and value\. This policy uses the `s3:ExistingObjectTag` condition key to specify the tag key and value\.  

```
{
  "Version": "2012-10-17",
  "Statement": [
  {
    "Principal": {
      "AWS": [
        "arn:aws:iam::111122223333:role/JohnDoe"
      ]
    },
    "Effect": "Allow",
    "Action": ["s3:GetObject", "s3:GetObjectVersion"],
    "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/*",
    "Condition": {
      "StringEquals": 
        {"s3:ExistingObjectTag/environment": "production"}
    }
  }
  ]
}
```

**Example 2: Restrict which object tag keys that users can add**  
The following permissions policy grants a user permissions to perform the `s3:PutObjectTagging` action, which allows user to add tags to an existing object\. The condition uses the `s3:RequestObjectTagKeys` condition key to specify the allowed tag keys, such as `Owner` or `CreationDate`\. For more information, see [Creating a condition that tests multiple key values](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_multi-value-conditions.html) in the *IAM User Guide*\.  
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

**Example 3: Require a specific tag key and value when allowing users to add object tags**  
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

