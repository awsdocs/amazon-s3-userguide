# User and role policy examples<a name="example-policies-s3"></a>

This section shows several example AWS Identity and Access Management \(IAM\) user and role policies for controlling access to Amazon S3\. For example *bucket policies*, see [Using bucket policies](bucket-policies.md)\. For information about IAM policy language, see [Bucket policies and user policies](using-iam-policies.md)\.

The following example policies will work if you use them programmatically\. However, to use them with the Amazon S3 console, you must grant additional permissions that are required by the console\. For information about using policies such as these with the Amazon S3 console, see [Controlling access to a bucket with user policies](walkthrough1.md)\. 

**Topics**
+ [Allowing an IAM user access to one of your buckets](#iam-policy-ex0)
+ [Allowing each IAM user access to a folder in a bucket](#iam-policy-ex1)
+ [Allowing a group to have a shared folder in Amazon S3](#iam-policy-ex2)
+ [Allowing all your users to read objects in a portion of a bucket](#iam-policy-ex3)
+ [Allowing a partner to drop files into a specific portion of a bucket](#iam-policy-ex4)
+ [Restricting access to Amazon S3 buckets within a specific AWS account](#iam-policy-ex6)
+ [Restricting access to Amazon S3 buckets within your organizational unit \(OU\)](#iam-policy-ex7)
+ [Restricting access to Amazon S3 buckets within your organization](#iam-policy-ex8)

## Allowing an IAM user access to one of your buckets<a name="iam-policy-ex0"></a>

In this example, you want to grant an IAM user in your AWS account access to one of your buckets, *DOC\-EXAMPLE\-BUCKET1*, and allow the user to add, update, and delete objects\. 

In addition to granting the `s3:PutObject`, `s3:GetObject`, and `s3:DeleteObject` permissions to the user, the policy also grants the `s3:ListAllMyBuckets`, `s3:GetBucketLocation`, and `s3:ListBucket` permissions\. These are the additional permissions required by the console\. Also, the `s3:PutObjectAcl` and the `s3:GetObjectAcl` actions are required to be able to copy, cut, and paste objects in the console\. For an example walkthrough that grants permissions to users and tests them using the console, see [Controlling access to a bucket with user policies](walkthrough1.md)\. 

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action": "s3:ListAllMyBuckets",
         "Resource":"*"
      },
      {
         "Effect":"Allow",
         "Action":["s3:ListBucket","s3:GetBucketLocation"],
         "Resource":"arn:aws:s3:::DOC-EXAMPLE-BUCKET1"
      },
      {
         "Effect":"Allow",
         "Action":[
            "s3:PutObject",
            "s3:PutObjectAcl",
            "s3:GetObject",
            "s3:GetObjectAcl",
            "s3:DeleteObject"
         ],
         "Resource":"arn:aws:s3:::DOC-EXAMPLE-BUCKET1/*"
      }
   ]
}
```

## Allowing each IAM user access to a folder in a bucket<a name="iam-policy-ex1"></a>

In this example, you want two IAM users, Mary and Carlos, to have access to your bucket, *DOC\-EXAMPLE\-BUCKET1*, so that they can add, update, and delete objects\. However, you want to restrict each user's access to a single prefix \(folder\) in the bucket\. You might create folders with names that match their user names\. 

```
DOC-EXAMPLE-BUCKET1
   Mary/
   Carlos/
```

To grant each user access only to their folder, you can write a policy for each user and attach it individually\. For example, you can attach the following policy to the user Mary to allow her specific Amazon S3 permissions on the `DOC-EXAMPLE-BUCKET1/Mary` folder\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":[
            "s3:PutObject",
            "s3:GetObject",
            "s3:GetObjectVersion",
            "s3:DeleteObject",
            "s3:DeleteObjectVersion"
         ],
         "Resource":"arn:aws:s3:::DOC-EXAMPLE-BUCKET1/Mary/*"
      }
   ]
}
```

You can then attach a similar policy to the user Carlos, specifying the folder `Carlos` in the `Resource` value\.

Instead of attaching policies to individual users, you can write a single policy that uses a policy variable and then attach the policy to a group\. First, you must create a group and add both Mary and Carlos to the group\. The following example policy allows a set of Amazon S3 permissions in the `DOC-EXAMPLE-BUCKET1/${aws:username}` folder\. When the policy is evaluated, the policy variable `${aws:username}` is replaced by the requester's user name\. For example, if Mary sends a request to put an object, the operation is allowed only if Mary is uploading the object to the `DOC-EXAMPLE-BUCKET1/Mary` folder\.

```
 1. {
 2.    "Version":"2012-10-17",
 3.    "Statement":[
 4.       {
 5.          "Effect":"Allow",
 6.          "Action":[
 7.             "s3:PutObject",
 8.             "s3:GetObject",
 9.             "s3:GetObjectVersion",
10.             "s3:DeleteObject",
11.             "s3:DeleteObjectVersion"
12.          ],
13.          "Resource":"arn:aws:s3:::DOC-EXAMPLE-BUCKET1/${aws:username}/*"
14.       }
15.    ]
16. }
```

**Note**  
When using policy variables, you must explicitly specify version `2012-10-17` in the policy\. The default version of the IAM policy language, 2008\-10\-17, does not support policy variables\. 

 If you want to test the preceding policy on the Amazon S3 console, the console requires additional permissions, as shown in the following policy\. For information about how the console uses these permissions, see [Controlling access to a bucket with user policies](walkthrough1.md)\. 

```
{
 "Version":"2012-10-17",
  "Statement": [
    {
      "Sid": "AllowGroupToSeeBucketListInTheConsole",
      "Action": [ 
      	"s3:ListAllMyBuckets", 
      	"s3:GetBucketLocation" 
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:s3:::*"  
    },
    {
      "Sid": "AllowRootLevelListingOfTheBucket",
      "Action": "s3:ListBucket",
      "Effect": "Allow",
      "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET1",
      "Condition":{ 
            "StringEquals":{
                    "s3:prefix":[""], "s3:delimiter":["/"]
                           }
                 }
    },
    {
      "Sid": "AllowListBucketOfASpecificUserPrefix",
      "Action": "s3:ListBucket",
      "Effect": "Allow",
      "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET1",
      "Condition":{  "StringLike":{"s3:prefix":["${aws:username}/*"] }
       }
    },
      {
     "Sid": "AllowUserSpecificActionsOnlyInTheSpecificUserPrefix",
         "Effect":"Allow",
         "Action":[
            "s3:PutObject",
            "s3:GetObject",
            "s3:GetObjectVersion",
            "s3:DeleteObject",
            "s3:DeleteObjectVersion"
         ],
         "Resource":"arn:aws:s3:::DOC-EXAMPLE-BUCKET1/${aws:username}/*"
      }
  ]
}
```

**Note**  
In the 2012\-10\-17 version of the policy, policy variables start with `$`\. This change in syntax can potentially create a conflict if your object key \(object name\) includes a `$`\.   
To avoid this conflict, specify the `$` character by using `${$}`\. For example, to include the object key `my$file` in a policy, specify it as `my${$}file`\.

Although IAM user names are friendly, human\-readable identifiers, they aren't required to be globally unique\. For example, if the user Carlos leaves the organization and another Carlos joins, then the new Carlos could access the old Carlos's information\.

Instead of using user names, you could create folders based on IAM user IDs\. Each IAM user ID is unique\. In this case, you must modify the preceding policy to use the `${aws:userid}` policy variable\. For more information about user identifiers, see [IAM Identifiers](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_identifiers.html) in the *IAM User Guide*\.

```
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect":"Allow",
         "Action":[
            "s3:PutObject",
            "s3:GetObject",
            "s3:GetObjectVersion",
            "s3:DeleteObject",
            "s3:DeleteObjectVersion"
         ],
         "Resource":"arn:aws:s3:::DOC-EXAMPLE-BUCKET1/home/${aws:userid}/*"
      }
   ]
}
```

### Allowing non\-IAM users \(mobile app users\) access to folders in a bucket<a name="non-iam-mobile-app-user-access"></a>

Suppose that you want to develop a mobile app, a game that stores users' data in an S3 bucket\. For each app user, you want to create a folder in your bucket\. You also want to limit each user's access to their own folder\. But you cannot create folders before someone downloads your app and starts playing the game, because you don't have their user ID\.

In this case, you can require users to sign in to your app by using public identity providers such as Login with Amazon, Facebook, or Google\. After users have signed in to your app through one of these providers, they have a user ID that you can use to create user\-specific folders at runtime\.

You can then use web identity federation in AWS Security Token Service to integrate information from the identity provider with your app and to get temporary security credentials for each user\. You can then create IAM policies that allow the app to access your bucket and perform such operations as creating user\-specific folders and uploading data\. For more information about web identity federation, see [About web identity Federation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_oidc.html) in the *IAM User Guide*\.

## Allowing a group to have a shared folder in Amazon S3<a name="iam-policy-ex2"></a>

Attaching the following policy to the group grants everybody in the group access to the following folder in Amazon S3: `DOC-EXAMPLE-BUCKET1/share/marketing`\. Group members are allowed to access only the specific Amazon S3 permissions shown in the policy and only for objects in the specified folder\. 

```
 1. {
 2.    "Version":"2012-10-17",
 3.    "Statement":[
 4.       {
 5.          "Effect":"Allow",
 6.          "Action":[
 7.             "s3:PutObject",
 8.             "s3:GetObject",
 9.             "s3:GetObjectVersion",
10.             "s3:DeleteObject",
11.             "s3:DeleteObjectVersion"
12.          ],
13.          "Resource":"arn:aws:s3:::DOC-EXAMPLE-BUCKET1/share/marketing/*"
14.       }
15.    ]
16. }
```

## Allowing all your users to read objects in a portion of a bucket<a name="iam-policy-ex3"></a>

In this example, you create a group named `AllUsers`, which contains all the IAM users that are owned by the AWS account\. You then attach a policy that gives the group access to `GetObject` and `GetObjectVersion`, but only for objects in the `DOC-EXAMPLE-BUCKET1/readonly` folder\. 

```
 1. {
 2.    "Version":"2012-10-17",
 3.    "Statement":[
 4.       {
 5.          "Effect":"Allow",
 6.          "Action":[
 7.             "s3:GetObject",
 8.             "s3:GetObjectVersion"
 9.          ],
10.          "Resource":"arn:aws:s3:::DOC-EXAMPLE-BUCKET1/readonly/*"
11.       }
12.    ]
13. }
```

## Allowing a partner to drop files into a specific portion of a bucket<a name="iam-policy-ex4"></a>

In this example, you create a group called `AnyCompany` that represents a partner company\. You create an IAM user for the specific person or application at the partner company that needs access, and then you put the user in the group\. 

You then attach a policy that gives the group `PutObject` access to the following folder in a bucket:

`DOC-EXAMPLE-BUCKET1/uploads/anycompany` 

You want to prevent the `AnyCompany` group from doing anything else with the bucket, so you add a statement that explicitly denies permission to any Amazon S3 actions except `PutObject` on any Amazon S3 resource in the AWS account\.

```
 1. {
 2.    "Version":"2012-10-17",
 3.    "Statement":[
 4.       {
 5.          "Effect":"Allow",
 6.          "Action":"s3:PutObject",
 7.          "Resource":"arn:aws:s3:::DOC-EXAMPLE-BUCKET1/uploads/anycompany/*"
 8.       },
 9.       {
10.          "Effect":"Deny",
11.          "Action":"s3:*",
12.          "NotResource":"arn:aws:s3:::DOC-EXAMPLE-BUCKET1/uploads/anycompany/*"
13.       }
14.    ]
15. }
```

## Restricting access to Amazon S3 buckets within a specific AWS account<a name="iam-policy-ex6"></a>

If you want to ensure that your Amazon S3 principals are accessing only the resources that are inside of a trusted AWS account, you can restrict access\. For example, this [identity\-based IAM policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_identity-vs-resource.html) uses a `Deny` effect to block access to Amazon S3 actions, unless the Amazon S3 resource that's being accessed is in account `222222222222`\. To prevent an IAM principal in an AWS account from accessing Amazon S3 objects outside of the account, attach the following IAM policy:

```
 1. {
 2.   "Version": "2012-10-17",
 3.   "Statement": [
 4.     {
 5.       "Sid": "DenyS3AccessOutsideMyBoundary",
 6.       "Effect": "Deny",
 7.       "Action": [
 8.         "s3:*"
 9.       ],
10.       "Resource": "*",
11.       "Condition": {
12.         "StringNotEquals": {
13.           "aws:ResourceAccount": [
14.             "222222222222"
15.           ]
16.         }
17.       }
18.     }
19.   ]
20. }
```

**Note**  
This policy does not replace your existing IAM access controls, because it doesn't grant any access\. Instead, this policy acts as an additional guardrail for your other IAM permissions, regardless of the permissions granted through other IAM policies\.

Make sure to replace account ID `222222222222` in the policy with your own AWS account\. To apply a policy to multiple accounts while still maintaining this restriction, replace the account ID with the `aws:PrincipalAccount` condition key\. This condition requires that the principal and the resource must be in the same account\.

## Restricting access to Amazon S3 buckets within your organizational unit \(OU\)<a name="iam-policy-ex7"></a>

If you have an [organizational unit \(OU\)](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_ous.html) set up in AWS Organizations, you might want to restrict Amazon S3 bucket access to a specific part of your organization\. In this example, we'll use the `aws:ResourceOrgPaths` key to restrict Amazon S3 bucket access to an OU in your organization\. For this example, the [OU ID](https://docs.aws.amazon.com/organizations/latest/APIReference/API_OrganizationalUnit.html) is `ou-acroot-exampleou`\. Make sure to replace this value in your own policy with your own OU IDs\.

```
 1. {
 2.    "Version": "2012-10-17",
 3.    "Statement": [
 4.      {
 5.        "Sid": "AllowS3AccessOutsideMyBoundary",
 6.        "Effect": "Allow",
 7.        "Action": [
 8.          "s3:*"
 9.        ],
10.        "Resource": "*",
11.        "Condition": {
12.          "ForAllValues:StringNotLike": {
13.            "aws:ResourceOrgPaths": [
14.              "o-acorg/r-acroot/ou-acroot-exampleou/"
15.            ] 
16.          }
17.        }
18.      }
19.    ]
20.  }
```

**Note**  
This policy does not grant any access\. Instead, this policy acts as a backstop for your other IAM permissions, preventing your principals from accessing Amazon S3 objects outside of an OU\-defined boundary\.

The policy denies access to Amazon S3 actions unless the Amazon S3 object that's being accessed is in the `ou-acroot-exampleou` OU in your organization\. The [IAM policy condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html) requires `aws:ResourceOrgPaths`, a multivalued condition key, to contain any of the listed OU paths\. The policy uses the `ForAllValues:StringNotLike` operator to compare the values of `aws:ResourceOrgPaths` to the listed OUs without case\-sensitive matching\.

## Restricting access to Amazon S3 buckets within your organization<a name="iam-policy-ex8"></a>

To restrict access to Amazon S3 objects within your organization, attach an IAM policy to the root of the organization, applying it to all accounts in your organization\. To require your IAM principals to follow this rule, use a [service\-control policy \(SCP\)](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html)\. If you choose to use an SCP, make sure to thoroughly [test the SCP](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html#scp-warning-testing-effect) before attaching the policy to the root of the organization\.

In the following example policy, access is denied to Amazon S3 actions unless the Amazon S3 object that's being accessed is in the same organization as the IAM principal that is accessing it:

```
 1. {
 2.    "Version": "2012-10-17",
 3.    "Statement": [
 4.      {
 5.        "Sid": "DenyS3AccessOutsideMyBoundary",
 6.        "Effect": "Deny",
 7.        "Action": [
 8.          "s3:*"
 9.        ],
10.        "Resource": "arn:aws:s3:::*/*",
11.        "Condition": {
12.          "StringNotEquals": {
13.            "aws:ResourceOrgID": "${aws:PrincipalOrgID}"
14.          }
15.        }
16.      }
17.    ]
18.  }
```

**Note**  
This policy does not grant any access\. Instead, this policy acts as a backstop for your other IAM permissions, preventing your principals from accessing any Amazon S3 objects outside of your organization\. This policy also applies to Amazon S3 resources that are created after the policy is put into effect\.

The [IAM policy condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html) in this example requires `aws:ResourceOrgID` and `aws:PrincipalOrgID` to be equal to each other\. With this requirement, the principal making the request and the resource being accessed must be in the same organization\.