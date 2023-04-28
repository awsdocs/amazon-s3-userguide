# Access policy guidelines<a name="access-policy-alternatives-guidelines"></a>

Amazon S3 supports resource\-based policies and user policies to manage access to your Amazon S3 resources\. For more information, see [Managing access to resources](access-control-overview.md#access-control-resources-manage-permissions-basics)\. Resource\-based policies include bucket policies, bucket access control lists \(ACLs\), and object ACLs\. This section describes specific scenarios for using resource\-based access policies to manage access to your Amazon S3 resources\. 

**Topics**
+ [When to use an ACL\-based access policy \(bucket and object ACLs\)](#when-to-use-acl)
+ [When to use a bucket policy](#when-to-use-bucket-policy)
+ [When to use a user policy](#when-to-use-user-policy)
+ [Related topics](#access-control-guidelines-related-topics)

## When to use an ACL\-based access policy \(bucket and object ACLs\)<a name="when-to-use-acl"></a>

Both buckets and objects have associated ACLs that you can use to grant permissions\. 

S3 Object Ownership is an Amazon S3 bucket\-level setting that you can use to both control ownership of the objects that are uploaded to your bucket and to disable or enable ACLs\. By default, Object Ownership is set to the bucket owner enforced setting, and all ACLs are disabled\. When ACLs are disabled, the bucket owner owns all the objects in the bucket and manages access to them exclusively by using access\-management policies\.

 A majority of modern use cases in Amazon S3 no longer require the use of ACLs\. We recommend that you keep ACLs disabled, except in unusual circumstances where you need to control access for each object individually\. With ACLs disabled, you can use policies to control access to all objects in your bucket, regardless of who uploaded the objects to your bucket\. For more information, see [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\.

**Important**  
If your bucket uses the bucket owner enforced setting for S3 Object Ownership, you must use policies to grant access to your bucket and the objects in it\. With the bucket owner enforced setting enabled, requests to set access control lists \(ACLs\) or update ACLs fail and return the `AccessControlListNotSupported` error code\. Requests to read ACLs are still supported\.

### When to use an object ACL<a name="when-to-use-object-acl"></a>

The following are the scenarios when you would use object ACLs to manage permissions\.

**Objects are not owned by the bucket owner**  
An object ACL is the only way to manage access to objects that are not owned by the bucket owner\. An AWS account that owns the bucket can grant another AWS account permission to upload objects\. The bucket owner does not own these objects\. The AWS account that created the object must grant permissions using object ACLs\.

**Note**  
A bucket owner cannot grant permissions on objects it does not own\. For example, a bucket policy granting object permissions applies only to objects owned by the bucket owner\. However, the bucket owner, who pays the bills, can write a bucket policy to deny access to any objects in the bucket, regardless of who owns it\. The bucket owner can also delete any objects in the bucket\. 

**You need to manage permissions at the object level**  
Suppose that the permissions vary by object and you need to manage permissions at the object level\. You can write a single policy statement granting an AWS account read permission on millions of objects with a specific [key name prefix](https://docs.aws.amazon.com/general/latest/gr/glos-chap.html#keyprefix)\. For example, you could grant read permission on objects starting with the key name prefix `logs`\. However, if your access permissions vary by object, granting permissions to individual objects using a bucket policy might not be practical\. Also, the bucket policies are limited to 20 KB in size\.

In this case, you might find using object ACLs a good alternative\. However, even an object ACL is also limited to a maximum of 100 grants\. For more information, see [Access control list \(ACL\) overview](acl-overview.md)\.

**Object ACLs control only object\-level permissions**  
 There is a single bucket policy for the entire bucket, but object ACLs are specified per object\.

An AWS account that owns a bucket can grant another AWS account permission to manage an access policy\. Doing so allows that account to change anything in the policy\. To better manage permissions, you might choose not to give such a broad permission, and instead grant the other account only the `READ-ACP` and `WRITE-ACP` permissions on a subset of objects\. This limits the account to manage permissions only on specific objects by updating individual object ACLs\.

If you want to use ACLs to manage permissions at the object level and you also want to own new objects written to your bucket, you can apply the bucket owner preferred setting for Object Ownership\. A bucket with the bucket owner preferred setting continues to accept and honor bucket and object ACLs\. With this setting, new objects that are written with the `bucket-owner-full-control` canned ACL will be automatically owned by the bucket owner rather than the object writer\. All other ACL behaviors remain in place\. To require all Amazon S3 PUT operations to include the `bucket-owner-full-control` canned ACL, you can [add a bucket policy](ensure-object-ownership.md#ensure-object-ownership-bucket-policy) that allows only object uploads using this ACL\.

**Alternatives to using ACLs**  
In addition to an object ACL, there are other ways an object owner can manage object permissions:
+ If the AWS account that owns the object also owns the bucket, it can write a bucket policy to manage the object permissions\.
+ If the AWS account that owns the object wants to grant permission to a user in its account, it can use a user policy\.
+ If you, as the bucket owner, want to automatically own and have full control over every object in your bucket, you can apply the bucket owner enforced setting for Object Ownership to disable ACLs\. As a result, access control for your data is based on policies\. For more information, see [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\.

### When to use a bucket ACL<a name="when-to-use-bucket-acl"></a>

The only recommended use case for bucket ACLs is to grant permissions to certain AWS services like the Amazon CloudFront `awslogsdelivery` account\. When you create or update a distribution and enable CloudFront logging, CloudFront updates the bucket ACL to give the `awslogsdelivery` account `FULL_CONTROL` permissions to write logs to your bucket\. For more information, see [Permissions required to configure standard logging and to access your log files](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/AccessLogs.html#AccessLogsBucketAndFileOwnership) in the *Amazon CloudFront Developer Guide*\. If the bucket that stores the logs uses the bucket owner enforced setting for S3 Object Ownership to disable ACLs, CloudFront cannot write logs to the bucket\. For more information, see [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\.

## When to use a bucket policy<a name="when-to-use-bucket-policy"></a>

If an AWS account that owns a bucket wants to grant permission to users in its account, it can use either a bucket policy or a user policy\. However, in the following scenarios, you must use a bucket policy\.

**You want to manage cross\-account permissions for all Amazon S3 permissions**  
You can use ACLs to grant cross\-account permissions to other accounts\. But ACLs support only a finite set of permissions, and these don't include all Amazon S3 permissions\. For more information, see [What permissions can I grant?](acl-overview.md#permissions) For example, you can't grant permissions on bucket subresources\. For more information, see [Identity and access management in Amazon S3](s3-access-control.md)\.

Both bucket and user policies support granting permission for all Amazon S3 operations\. \(For more information, see [Amazon S3 actions](using-with-s3-actions.md)\.\) However, the user policies are for managing permissions for users in your account\. For cross\-account permissions to other AWS accounts or users in another account, you must use a bucket policy\.

## When to use a user policy<a name="when-to-use-user-policy"></a>

In general, you can use either a user policy or a bucket policy to manage permissions\. You can choose to manage permissions by creating users and managing permissions individually by attaching policies to users \(or user groups\)\. Or, you might find that resource\-based policies, such as a bucket policy, work better for your scenario\.

With AWS Identity and Access Management \(IAM\) you can create multiple users within your AWS account and manage their permissions through user policies\. An IAM user must have permissions from the parent account to which it belongs, and from the AWS account that owns the resource that the user wants to access\. The permissions can be granted as follows:
+ **Permission from the parent account** – The parent account can grant permissions to its user by attaching a user policy\.
+ **Permission from the resource owner** – The resource owner can grant permission to either the IAM user \(by using a bucket policy\) or the parent account \(by using a bucket policy, bucket ACL, or object ACL\)\.

This is similar to a child who wants to play with a toy that belongs to someone else\. To play with the toy, the child must get permission from a parent and permission from the toy owner\.

For more information, see [Bucket policies and user policies](using-iam-policies.md)\.

### Permission delegation<a name="permission-delegation"></a>

If an AWS account owns a resource, it can grant those permissions to another AWS account\. That account can then delegate those permissions, or a subset of them, to users in the account\. This is referred to as permission delegation\. But an account that receives permissions from another account cannot delegate permission cross\-account to another AWS account\. 

## Related topics<a name="access-control-guidelines-related-topics"></a>

We recommend that you first review all introductory topics that explain how you manage access to your Amazon S3 resources and related guidelines\. For more information, see [Identity and access management in Amazon S3](s3-access-control.md)\. You can then use the following topics for more information about specific access policy options\. 
+ [Access control list \(ACL\) overview](acl-overview.md)
+ [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)