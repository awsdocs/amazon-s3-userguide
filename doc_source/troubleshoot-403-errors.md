# Troubleshoot Access Denied \(403 Forbidden\) errors in Amazon S3<a name="troubleshoot-403-errors"></a>

The following topics cover the most common causes of Access Denied \(403 Forbidden\) errors in Amazon S3\.

**Topics**
+ [Bucket policies and IAM policies](#bucket-iam-policies)
+ [Amazon S3 ACL settings](#troubleshoot-403-acl-settings)
+ [S3 Block Public Access settings](#troubleshoot-403-bpa)
+ [Amazon S3 encryption settings](#troubleshoot-403-encryption)
+ [S3 Object Lock settings](#troubleshoot-403-object-lock)
+ [VPC endpoint policy](#troubleshoot-403-vpc)
+ [AWS Organizations policies](#troubleshoot-403-orgs)
+ [Access point settings](#troubleshoot-403-access-points)

## Bucket policies and IAM policies<a name="bucket-iam-policies"></a>

### Bucket\-level operations<a name="troubleshoot-403-bucket-level-ops"></a>

If there is no bucket policy in place, then the bucket implicitly allows requests from any AWS Identity and Access Management \(IAM\) identity in the bucket\-owning account\. The bucket also implicitly denies requests from any other IAM identities from any other accounts, and anonymous \(unsigned\) requests\. However, if there is no IAM user policy in place, the requester \(unless they are the root user\) is implicitly denied from making any requests\. For more information about this evaluation logic, see [Determining whether a request is denied or allowed within an account](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html#policy-eval-denyallow) in the *IAM User Guide*\.

### Object\-level operations<a name="troubleshoot-403-object-level-ops"></a>

If the object is owned by the bucket\-owning account, the bucket policy and IAM user policy will function in the same way for object\-level operations as they do for bucket\-level operations\. For example, if there is no bucket policy in place, then the bucket implicitly allows object requests from any IAM identity in the bucket\-owning account\. The bucket also implicitly denies object requests from any other IAM identities from any other accounts, and anonymous \(unsigned\) requests\. However, if there is no IAM user policy in place, the requester \(unless they are the root user\) is implicitly denied from making any object requests\.

If the object is owned by an external account, then access to the object can be granted only through object access control lists \(ACLs\)\. The bucket policy and IAM user policy can still be used to deny object requests\. 

Therefore, to ensure that your bucket policy or IAM user policy is not causing an Access Denied \(403 Forbidden\) error, make sure that the following requirements are met:
+ For same\-account access, there must not be an explicit `Deny` statement against the requester you are trying to grant permissions to, in either the bucket policy or the IAM user policy\. If you want to grant permissions by using only the bucket policy and the IAM user policy, there must be at least one explicit `Allow` statement in one of these policies\.
+ For cross\-account access, there must not be an explicit `Deny` statement against the requester you are trying to grant permissions to, in either the bucket policy or the IAM user policy\. If you want to grant cross\-account permissions by using only the bucket policy and IAM user policy, then both the bucket policy and the IAM user policy of the requester must include an explicit `Allow` statement\.

**Note**  
`Allow` statements in a bucket policy apply only to objects that are [owned by the same bucket\-owning account](https://docs.aws.amazon.com/AmazonS3/latest/userguide/about-object-ownership.html)\. However, `Deny` statements in a bucket policy apply to all objects regardless of object ownership\. 

**To review or edit your bucket policy**
**Note**  
To view or edit a bucket policy, you must have the `s3:GetBucketPolicy` permission\.

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. From the **Buckets** list, choose the name of the bucket that you want to view or edit a bucket policy for\.

1. Choose the **Permissions** tab\.

1. Under **Bucket policy**, choose **Edit**\. The **Edit bucket policy** page appears\.

To review or edit your bucket policy by using the AWS Command Line Interface \(AWS CLI\), use the [https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/get-bucket-policy.html](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/get-bucket-policy.html) command\.

**Note**  
If you get locked out of a bucket because of an incorrect bucket policy, [sign in to the AWS Management Console by using your root user credentials\.](https://docs.aws.amazon.com/signin/latest/userguide/introduction-to-root-user-sign-in-tutorial.html) To regain access to your bucket, make sure to delete the bucket policy by using your root user credentials\.

### Tips for checking permissions<a name="troubleshoot-403-tips"></a>

To check whether the requester has proper permissions to perform an Amazon S3 operation, try the following:
+ Identify the requester\. If it’s an unsigned request, then it's an anonymous request without an IAM user policy\. If it’s a request using a presigned URL, then the user policy will be the same as the one for the IAM user or role that signed the request\.
+ Verify that you're using the correct IAM user or role\. You can verify your IAM user or role by checking the upper\-right corner of the AWS Management Console or by using the [https://docs.aws.amazon.com/cli/latest/reference/sts/get-caller-identity.html](https://docs.aws.amazon.com/cli/latest/reference/sts/get-caller-identity.html) command\.
+ Check the IAM policies that are related to the IAM user or role\. You can use one of the following methods:
  + [Test IAM policies with the IAM policy simulator](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_testing-policies.html)\.
  + Review the different [IAM policy types](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html)\.
+ If needed, [edit your IAM user policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_manage-edit.html)\.
+ Review the following examples of policies that explicitly deny or allow access:
  + Explicit allow IAM user policy: [IAM: Allows and denies access to multiple services programmatically and in the console](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_iam_multiple-services-console.html)
  + Explicit allow bucket policy: [Granting permissions to multiple accounts to upload objects or set object ACLs for public access](https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-bucket-policies.html#example-bucket-policies-acl-1)
  + Explicit deny IAM user policy: [AWS: Denies access to AWS based on the requested AWS Region](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_aws_deny-requested-region.html)
  + Explicit deny bucket policy: [Require SSE\-KMS for all objects written to a bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-bucket-policies.html#example-bucket-policies-encryption-1)

## Amazon S3 ACL settings<a name="troubleshoot-403-acl-settings"></a>

**Important**  
A majority of use cases in Amazon S3 no longer require the use of access control lists \(ACLs\)\. We recommend that you disable ACLs, except in unusual circumstances where you need to control access for each object individually\. Starting in April 2023, Amazon S3 will change the default setting for S3 Object Ownership for all new buckets\. For new buckets created after this update, ACLs will be disabled\. This new default follows the recommended best practices for securing data in Amazon S3\. You can adjust the Object Ownership setting after creating your bucket\. For more information, see [ Default settings for new S3 buckets FAQ](create-bucket-faq.md) and [Heads\-up: Amazon S3 security changes are coming in April of 2023](http://aws.amazon.com/blogs/aws/heads-up-amazon-s3-security-changes-are-coming-in-april-of-2023/) in the *AWS News Blog*\. 

When checking your ACL settings, first [review your Object Ownership setting](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-ownership-retrieving.html) to check whether ACLs are enabled on the bucket\. Be aware that ACL permissions can be used only to grant permissions and cannot be used to reject requests\. ACLs also cannot be used to grant access to requesters that are rejected by explicit denials in bucket policies or IAM user policies\.

### The Object Ownership setting is set to bucket owner enforced<a name="troubleshoot-403-object-ownership-1"></a>

If the bucket owner enforced setting is enabled, then ACL settings are unlikely to cause an Access Denied \(403 Forbidden\) error because this setting disables all ACLs that apply to bucket and objects\. Bucket owner enforced is the default \(and recommended\) setting for Amazon S3 buckets\.

### The Object Ownership setting is set to bucket owner preferred or object writer<a name="troubleshoot-403-object-ownership-2"></a>

ACL permissions are still valid with the bucket owner preferred setting or the object writer setting\. There are two kinds of ACLs: bucket ACLs and object ACLs\. For the differences between these two types of ACLs, see [Mapping of ACL permissions and access policy permissions](https://docs.aws.amazon.com/AmazonS3/latest/userguide/acl-overview.html#acl-access-policy-permission-mapping)\.

Depending on the action of the rejected request, [check the ACL permissions for your bucket or the object](https://docs.aws.amazon.com/AmazonS3/latest/userguide/managing-acls.html):
+ If Amazon S3 rejected a `LIST`, `PUT` object, `GetBucketAcl`, or `PutBucketAcl` request, then [review the ACL permissions for your bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/managing-acls.html)\.
**Note**  
You cannot grant `GET` object permissions with bucket ACL settings\.
+ If Amazon S3 rejected a `GET` request on an S3 object, or a [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectAcl.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectAcl.html) request, then [review the ACL permissions for the object](https://docs.aws.amazon.com/AmazonS3/latest/userguide/managing-acls.html)\.
**Important**  
If the account that owns the object is different from the account that owns the bucket, then access to the object isn't controlled by the bucket policy\.

### Troubleshooting an Access Denied \(403 Forbidden\) error from a `GET` object request during cross\-account object ownership<a name="troubleshoot-403-object-ownership-tips"></a>

Review the bucket's [Object Ownership settings](https://docs.aws.amazon.com/AmazonS3/latest/userguide/about-object-ownership.html#object-ownership-overview) to determine the object owner\. If you have access to the [object ACLs](https://docs.aws.amazon.com/AmazonS3/latest/userguide/managing-acls.html), then you can also check the object owner's account\. \(To view the object owner's account, review the object ACL setting in the Amazon S3 console\.\) Alternatively, you can also make a `GetObjectAcl` request to find the object owner’s [canonical ID](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObjectAcl.html) to verify the object owner account\. By default, ACLs grant explicit allow permissions for `GET` requests to the object owner’s account\.

After you've confirmed that the object owner is different from the bucket owner, then depending on your use case and access level, choose one of the following methods to help address the Access Denied \(403 Forbidden\) error:
+ **Disable ACLs \(recommended\)** – This method will apply to all objects and can be performed by the bucket owner\. This method automatically gives the bucket owner ownership and full control over every object in the bucket\. Before you implement this method, check the [prerequisites for disabling ACLs](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-ownership-migrating-acls-prerequisites.html)\. For information about how to set your bucket to bucket owner enforced \(recommended\) mode, see [Setting Object Ownership on an existing bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-ownership-existing-bucket.html)\.
**Important**  
To prevent an Access Denied \(403 Forbidden\) error, be sure to migrate the ACL permissions to a bucket policy before you disable ACLs\. For more information, see [Bucket policy examples for migrating from ACL permissions](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-ownership-migrating-acls-prerequisites.html#migrate-acl-permissions-bucket-policies)\.
+ **Change the object owner to the bucket owner** – This method can be applied to individual objects, but only the object owner \(or a user with the appropriate permissions\) can change an object's ownership\. Additional PUT costs might apply\. \(For more information, see [Amazon S3 pricing](https://aws.amazon.com/s3/pricing/)\.\) This method grants the bucket owner full ownership of the object, allowing the bucket owner to control access to the object through a bucket policy\. 

  To change the object's ownership, do one of the following:
  + You \(the bucket owner\) can [copy the object](https://docs.aws.amazon.com/AmazonS3/latest/userguide/copy-object.html#CopyingObjectsExamples) back to the bucket\. 
  + You can change the Object Ownership setting of the bucket to bucket owner preferred\. If versioning is disabled, the objects in the bucket are overwritten\. If versioning is enabled, duplicate versions of the same object will appear in the bucket, which the bucket owner can [set a lifecycle rule to expire](https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-expire-general-considerations.html)\. For instructions on how to change your Object Ownership setting, see [Setting Object Ownership on an existing bucket](object-ownership-existing-bucket.md)\.
**Note**  
When you update your Object Ownership setting to bucket owner preferred, the setting is only applied to new objects that are uploaded to the bucket\.
  + You can have the object owner upload the object again with the `bucket-owner-full-control` canned object ACL\. 
**Note**  
For cross\-account uploads, you can also require the `bucket-owner-full-control` canned object ACL in your bucket policy\. For an example bucket policy, see [Grant cross\-account permissions to upload objects while ensuring that the bucket owner has full control](https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-bucket-policies.html#example-bucket-policies-acl-2)\.
+ **Keep the object writer as the object owner** – This method doesn't change the object owner, but it does allow you to grant access to objects individually\. To grant access to an object, you must have the `PutObjectAcl` permission for the object\. Then, to fix the Access Denied \(403 Forbidden\) error, add the requester as a [grantee](https://docs.aws.amazon.com/AmazonS3/latest/userguide/acl-overview.html#specifying-grantee) to access the object in the object's ACLs\. For more information, see [Configuring ACLs](managing-acls.md)\.

## S3 Block Public Access settings<a name="troubleshoot-403-bpa"></a>

If the failed request involves public access or public policies, then check the S3 Block Public Access settings on your account, bucket, or S3 access point\. Starting in April 2023, all Block Public Access settings are enabled by default for new buckets\. For more information about how Amazon S3 defines "public," see [The meaning of "public"](access-control-block-public-access.md#access-control-block-public-access-policy-status)\.

When set to `TRUE`, Block Public Access settings act as explicit deny policies that override permissions allowed by ACLs, bucket policies, and IAM user policies\. To determine whether your Block Public Access settings are rejecting your request, review the following scenarios:
+ If the specified access control list \(ACL\) is public, then the `BlockPublicAcls` setting rejects your `PutBucketAcl` and `PutObjectACL` calls\.
+ If the request includes a public ACL, then the `BlockPublicAcls` setting rejects your `PutObject` calls\.
+ If the `BlockPublicAcls` setting is applied to an account and the request includes a public ACL, then any `CreateBucket` calls that include public ACLs will fail\.
+ If your request's permission is granted only by a public ACL, then the `IgnorePublicAcls` setting rejects the request\.
+ If the specified bucket policy allows public access, then the `BlockPublicPolicy` setting rejects your `PutBucketPolicy` calls\.
+ If the `BlockPublicPolicy` setting is applied to an access point, then all `PutAccessPointPolicy` and `PutBucketPolicy` calls that specify a public policy and are made through the access point will fail\.
+ If the access point or bucket has a public policy, then the `RestrictPublicBuckets` setting rejects all cross\-account calls except for AWS service principals\. This setting also rejects all anonymous \(or unsigned\) calls\.

To review and update your Block Public Access setting configurations, see [Configuring block public access settings for your S3 buckets](configuring-block-public-access-bucket.md)\.

## Amazon S3 encryption settings<a name="troubleshoot-403-encryption"></a>

Amazon S3 supports server\-side encryption on your bucket\. Server\-side encryption is the encryption of data at its destination by the application or service that receives it\. Amazon S3 encrypts your data at the object level as it writes it to disks in AWS data centers and decrypts it for you when you access it\. 

By default, Amazon S3 now applies server\-side encryption with Amazon S3 managed keys \(SSE\-S3\) as the base level of encryption for every bucket in Amazon S3\. Amazon S3 also allows you to specify the server\-side encryption method when uploading objects\.

**To review your bucket's server\-side encryption status and encryption settings**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. From the **Buckets** list, choose the bucket that you want to check the encryption settings for\.

1. Choose the **Properties** tab\.

1. Scroll down to the **Default encryption** section and view the **Encryption key type** settings\.

To check your encryption settings by using the AWS CLI, use the [https://docs.aws.amazon.com/cli/latest/reference/s3api/get-bucket-encryption.html](https://docs.aws.amazon.com/cli/latest/reference/s3api/get-bucket-encryption.html) command\.

**To check the encryption status of an object**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. From the **Buckets** list, choose the name of the bucket that contains the object\.

1. From the **Objects** list, choose the name of the object that you want to add or change encryption for\. 

   The object's details page appears\.

1. Scroll down to the **Server\-side encryption settings** section to view the object's server\-side encryption settings\.

To check your object encryption status by using the AWS CLI, use the [https://docs.aws.amazon.com/cli/latest/reference/s3api/head-object.html#examples](https://docs.aws.amazon.com/cli/latest/reference/s3api/head-object.html#examples) command\.

### Encryption and permissions requirements<a name="troubleshoot-403-encryption-requirements"></a>

Amazon S3 supports three types of server\-side encryption:
+ Server\-side encryption with Amazon S3 managed keys \(SSE\-S3\)
+ Server\-side encryption with AWS Key Management Service \(AWS KMS\) keys \(SSE\-KMS\)
+ Server\-side encryption with customer\-provided keys \(SSE\-C\)

Based on your encryption settings, make sure that the following permissions requirements are met:
+ **SSE\-S3** – No extra permissions are required\.
+ **SSE\-KMS \(with a customer managed key\)** – To upload objects, the `kms:GenerateDataKey` permission on the AWS KMS key is required\. To download objects and perform multipart uploads of objects, the `kms:Decrypt` permission on the KMS key is required\.
+ **SSE\-KMS \(with an AWS managed key\)** – The requester must be from the same account that owns the `aws/s3` KMS key\. The requester must also have the correct Amazon S3 permissions to access the object\.
+ **SSE\-C \(with a customer provided key\)** – No additional permissions are required\. You can configure the bucket policy to [require and restrict server\-side encryption with customer\-provided encryption keys](https://docs.aws.amazon.com/AmazonS3/latest/userguide/ServerSideEncryptionCustomerKeys.html#ssec-require-condition-key) for objects in your bucket\.

If the object is encrypted with a customer managed key, make sure that the KMS key policy allows you to perform the `kms:GenerateDataKey` or `kms:Decrypt` actions\. For instructions on checking your KMS key policy, see [Viewing a key policy](https://docs.aws.amazon.com/kms/latest/developerguide/key-policy-viewing.html) in the *AWS Key Management Service Developer Guide*\.

## S3 Object Lock settings<a name="troubleshoot-403-object-lock"></a>

If your bucket has [S3 Object Lock](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock.html) enabled and the object is protected by a [retention period](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock-overview.html#object-lock-retention-periods) or [legal hold](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock-overview.html#object-lock-legal-holds), Amazon S3 returns an Access Denied \(403 Forbidden\) error when you try to delete the object\.

**To check whether the bucket has Object Lock enabled**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. From the **Buckets** list, choose the name of the bucket that you want to review\.

1. Choose the **Properties** tab\.

1. Scroll down to the **Object Lock** section\. Verify whether the **Object Lock** setting is **Enabled** or **Disabled**\.

To determine whether the object is protected by a retention period or legal hold, [view the lock information](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock-managing.html#object-lock-managing-view) for your object\. 

If the object is protected by a retention period or legal hold, check the following:
+ If the object version is protected by the compliance retention mode, there is no way to permanently delete it\. A permanent `DELETE` request from any requester, including the root user, will result in an Access Denied \(403 Forbidden\) error\. Also, be aware that when you submit a `DELETE` request for an object that's protected by the compliance retention mode, Amazon S3 creates a [delete marker](https://docs.aws.amazon.com/AmazonS3/latest/userguide/DeleteMarker.html) for the object\.
+ If the object version is protected with governance retention mode and you have the `s3:BypassGovernanceRetention` permission, you can bypass the protection and permanently delete the version\. For more information, see [Bypassing governance mode](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock-managing.html#object-lock-managing-bypass)\.
+ If the object version is protected by a legal hold, then a permanent `DELETE` request can result in an Access Denied \(403 Forbidden\) error\. To permanently delete the object version, you must remove the legal hold on the object version\. To remove a legal hold, you must have the `s3:PutObjectLegalHold` permission\. For more information about removing a legal hold, see [Configuring S3 Object Lock using the console](object-lock-console.md)\.

## VPC endpoint policy<a name="troubleshoot-403-vpc"></a>

If you're accessing Amazon S3 by using a virtual private cloud \(VPC\) endpoint, make sure that the VPC endpoint policy is not blocking you from accessing your Amazon S3 resources\. By default, the VPC endpoint policy allows all requests to Amazon S3\. You can also configure the VPC endpoint policy to restrict certain requests\. For information about how to check your VPC endpoint policy, see [Control access to VPC endpoints by using endpoint policies](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints-access.html) in the *AWS PrivateLink Guide*\.

## AWS Organizations policies<a name="troubleshoot-403-orgs"></a>

If your AWS account belongs to an organization, AWS Organizations policies can block you from accessing Amazon S3 resources\. By default, AWS Organizations policies do not block any requests to Amazon S3\. However, make sure that your AWS Organizations policies haven’t been configured to block access to S3 buckets\. For instructions on how to check your AWS Organizations policies, see [Listing all policies](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_info-operations.html#list-all-pols-in-org) in the *AWS Organizations User Guide*\.

## Access point settings<a name="troubleshoot-403-access-points"></a>

If you receive an Access Denied \(403 Forbidden\) error while making requests through Amazon S3 access points, you might need to check the following: 
+ The configurations for your access points
+ The IAM user policy that's used for your access points
+ The bucket policy that's used to manage or configure your cross\-account access points



**Access point configurations and policies**
+ When you create an access point, you can choose to designate **Internet** or **VPC** as the network origin\. If the network origin is set to VPC only, Amazon S3 will reject any requests made to the access point that don't originate from the specified VPC\. To check the network origin of your access point, see [Creating access points restricted to a virtual private cloud](access-points-vpc.md)\.
+ With access points, you can also configure custom Block Public Access settings, which work similarly to the Block Public Access settings at the bucket or account level\. To check your custom Block Public Access settings, see [Managing public access to access points](access-points-bpa-settings.md)\.
+ To make successful requests to Amazon S3 by using access points, make sure that the requester has the necessary IAM permissions\. For more information, see [Configuring IAM policies for using access points](access-points-policies.md)\.
+ If the request involves cross\-account access points, make sure that the bucket owner has updated the bucket policy to authorize requests from the access point\. For more information, see [Granting permissions for cross\-account access points](access-points-policies.md#access-points-cross-account)\.

If the Access Denied \(403 Forbidden\) error still persists after checking all the items in this topic, [retrieve your Amazon S3 request ID](https://docs.aws.amazon.com/AmazonS3/latest/userguide/get-request-ids.html) and contact AWS Support for additional guidance\.