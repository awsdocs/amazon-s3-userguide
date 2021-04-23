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

**Example 1: Allow a user to read only the objects that have a specific tag**  
The following permissions policy grants a user permission to read objects, but the condition limits the read permission to only objects that have the following specific tag key and value\.  

```
security : public
```
Note that the policy uses the Amazon S3 condition key, `s3:ExistingObjectTag/<tag-key>` to specify the key and value\.  

```
 1. {
 2.   "Version": "2012-10-17",
 3.   "Statement": [
 4.     {
 5.       "Effect":     "Allow",
 6.       "Action":     "s3:GetObject",
 7.       "Resource":    "arn:aws:s3:::awsexamplebucket1/*",
 8.       "Principal":   "*",
 9.       "Condition": {  "StringEquals": {"s3:ExistingObjectTag/security": "public" } }
10.     }
11.   ]
12. }
```

**Example 2: Allow a user to add object tags with restrictions on the allowed tag keys**  
The following permissions policy grants a user permissions to perform the `s3:PutObjectTagging` action, which allows user to add tags to an existing object\. The condition limits the tag keys that the user is allowed to use\. The condition uses the `s3:RequestObjectTagKeys` condition key to specify the set of tag keys\.  

```
 1. {
 2.   "Version": "2012-10-17",
 3.   "Statement": [
 4.     {
 5.       "Effect": "Allow",
 6.       "Action": [
 7.         "s3:PutObjectTagging"
 8.       ],
 9.       "Resource": [
10.         "arn:aws:s3:::awsexamplebucket1/*"
11.       ],
12.       "Principal":{
13.         "CanonicalUser":[
14.             "64-digit-alphanumeric-value"
15.          ]
16.        },
17.       "Condition": {
18.         "ForAllValues:StringLike": {
19.           "s3:RequestObjectTagKeys": [
20.             "Owner",
21.             "CreationDate"
22.           ]
23.         }
24.       }
25.     }
26.   ]
27. }
```
The policy ensures that the tag set, if specified in the request, has the specified keys\. A user might send an empty tag set in `PutObjectTagging`, which is allowed by this policy \(an empty tag set in the request removes any existing tags on the object\)\. If you want to prevent a user from removing the tag set, you can add another condition to ensure that the user provides at least one value\. The `ForAnyValue` in the condition ensures at least one of the specified values must be present in the request\.  

```
 1. {
 2. 
 3.   "Version": "2012-10-17",
 4.   "Statement": [
 5.     {
 6.       "Effect": "Allow",
 7.       "Action": [
 8.         "s3:PutObjectTagging"
 9.       ],
10.       "Resource": [
11.         "arn:aws:s3:::awsexamplebucket1/*"
12.       ],
13.       "Principal":{
14.         "AWS":[
15.             "arn:aws:iam::account-number-without-hyphens:user/username"
16.          ]
17.        },
18.       "Condition": {
19.         "ForAllValues:StringLike": {
20.           "s3:RequestObjectTagKeys": [
21.             "Owner",
22.             "CreationDate"
23.           ]
24.         },
25.         "ForAnyValue:StringLike": {
26.           "s3:RequestObjectTagKeys": [
27.             "Owner",
28.             "CreationDate"
29.           ]
30.         }
31.       }
32.     }
33.   ]
34. }
```
For more information, see [Creating a Condition That Tests Multiple Key Values \(Set Operations\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_multi-value-conditions.html) in the *IAM User Guide*\.

**Example 3: Allow a user to add object tags that include a specific tag key and value**  
The following user policy grants a user permissions to perform the `s3:PutObjectTagging` action, which allows user to add tags on an existing object\. The condition requires the user to include a specific tag \(`Project`\) with value set to `X`\.   

```
 1. {
 2.   "Version": "2012-10-17",
 3.   "Statement": [
 4.     {
 5.       "Effect": "Allow",
 6.       "Action": [
 7.         "s3:PutObjectTagging"
 8.       ],
 9.       "Resource": [
10.         "arn:aws:s3:::awsexamplebucket1/*"
11.       ],
12.       "Principal":{
13.         "AWS":[
14.             "arn:aws:iam::account-number-without-hyphens:user/username"
15.          ]
16.        },
17.       "Condition": {
18.         "StringEquals": {
19.           "s3:RequestObjectTag/Project": "X"
20.         }
21.       }
22.     }
23.   ]
24. }
```


