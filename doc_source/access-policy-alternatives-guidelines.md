--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Access policy guidelines<a name="access-policy-alternatives-guidelines"></a>

Amazon S3 supports resource\-based policies and user policies to manage access to your Amazon S3 resources\. For more information, see [Managing access to resources](access-control-overview.md#access-control-resources-manage-permissions-basics)\. Resource\-based policies include bucket policies, bucket access control lists \(ACLs\), and object ACLs\. This section describes specific scenarios for using resource\-based access policies to manage access to your Amazon S3 resources\. 

**Topics**
+ [When to use an ACL\-based access policy \(bucket and object ACLs\)](#when-to-use-acl)
+ [When to use a bucket policy](#when-to-use-bucket-policy)
+ [When to use a user policy](#when-to-use-user-policy)
+ [Related topics](#access-control-guidelines-related-topics)

## When to use an ACL\-based access policy \(bucket and object ACLs\)<a name="when-to-use-acl"></a>

Both buckets and objects have associated ACLs that you can use to grant permissions\. The following sections describe scenarios for using object ACLs and bucket ACLs\.

### When to use an object ACL<a name="when-to-use-object-acl"></a>

In addition to an object ACL, there are other ways an object owner can manage object permissions—for example:
+ If the AWS account that owns the object also owns the bucket, it can write a bucket policy to manage the object permissions\.
+ If the AWS account that owns the object wants to grant permission to a user in its account, it can use a user policy\.

So when do you use object ACLs to manage object permissions? The following are the scenarios when you would do so\.

**Objects are not owned by the bucket owner**  
An object ACL is the only way to manage access to objects that are not owned by the bucket owner\. An AWS account that owns the bucket can grant another AWS account permission to upload objects\. The bucket owner does not own these objects\. The AWS account that created the object must grant permissions using object ACLs\.

**Note**  
A bucket owner cannot grant permissions on objects it does not own\. For example, a bucket policy granting object permissions applies only to objects owned by the bucket owner\. However, the bucket owner, who pays the bills, can write a bucket policy to deny access to any objects in the bucket, regardless of who owns it\. The bucket owner can also delete any objects in the bucket\. 

**You need to manage permissions at the object level**  
Suppose that the permissions vary by object and you need to manage permissions at the object level\. You can write a single policy statement granting an AWS account read permission on millions of objects with a specific [key name prefix](https://docs.aws.amazon.com/general/latest/gr/glos-chap.html#keyprefix)\. For example, you could grant read permission on objects starting with key name prefix "logs"\. However, if your access permissions vary by object, granting permissions to individual objects using a bucket policy might not be practical\. Also the bucket policies are limited to 20 KB in size\.

In this case, you might find using object ACLs a good alternative\. However, even an object ACL is also limited to a maximum of 100 grants\. For more information, see [Access control list \(ACL\) overview](acl-overview.md)\.

**Object ACLs control only object\-level permissions**  
 There is a single bucket policy for the entire bucket, but object ACLs are specified per object\.

An AWS account that owns a bucket can grant another AWS account permission to manage access policy\. It allows that account to change anything in the policy\. To better manage permissions, you may choose not to give such a broad permission, and instead grant only the READ\-ACP and WRITE\-ACP permissions on a subset of objects\. This limits the account to manage permissions only on specific objects by updating individual object ACLs\.

### When to use a bucket ACL<a name="when-to-use-bucket-acl"></a>

The only recommended use case for the bucket ACL is to grant write permission to the Amazon S3 Log Delivery group to write access log objects to your bucket\. For more information, see [Logging requests using server access logging](ServerLogs.md)\. 

If you want Amazon S3 to deliver access logs to your bucket, you must grant write permission on the bucket to the Log Delivery group\. The only way you can grant necessary permissions to the Log Delivery group is via a bucket ACL, as shown in the following bucket ACL fragment\.

```
<?xml version="1.0" encoding="UTF-8"?>
<AccessControlPolicy xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
  <Owner>
   ...
  </Owner>
  <AccessControlList>
    <Grant>
        ...
    </Grant>  
    <Grant>
       <Grantee xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:type="Group">
        <URI>http://acs.amazonaws.com/groups/s3/LogDelivery</URI>
       </Grantee>
       <Permission>WRITE</Permission>
    </Grant>  
  </AccessControlList>
</AccessControlPolicy>
```

## When to use a bucket policy<a name="when-to-use-bucket-policy"></a>

If an AWS account that owns a bucket wants to grant permission to users in its account, it can use either a bucket policy or a user policy\. But in the following scenarios, you must use a bucket policy\.

**You want to manage cross\-account permissions for all Amazon S3 permissions**  
 You can use ACLs to grant cross\-account permissions to other accounts\. But ACLs support only a finite set of permissions, and these don't include all Amazon S3 permissions\. For more information, see [What permissions can I grant?](acl-overview.md#permissions) For example, you can't grant permissions on bucket subresources using an ACL\. For more information, see [Identity and access management in Amazon S3](s3-access-control.md)\.

Both bucket and user policies support granting permission for all Amazon S3 operations\. \(For more information, see [Amazon S3 actions](using-with-s3-actions.md)\.\) However, the user policies are for managing permissions for users in your account\. For cross\-account permissions to other AWS accounts or users in another account, you must use a bucket policy\.

## When to use a user policy<a name="when-to-use-user-policy"></a>

In general, you can use either a user policy or a bucket policy to manage permissions\. You can choose to manage permissions by creating users and managing permissions individually by attaching policies to users \(or user groups\)\. Or, you might find that resource\-based policies, such as a bucket policy, work better for your scenario\.

With AWS Identity and Access Management \(IAM\) you can create multiple users within your AWS account and manage their permissions via user policies\. An IAM user must have permissions from the parent account to which it belongs, and from the AWS account that owns the resource that the user wants to access\. The permissions can be granted as follows:
+ **Permission from the parent account** – The parent account can grant permissions to its user by attaching a user policy\.
+ **Permission from the resource owner** – The resource owner can grant permission to either the IAM user \(using a bucket policy\) or the parent account \(using a bucket policy, bucket ACL, or object ACL\)\.

This is similar to a child who wants to play with a toy that belongs to someone else\. To play with the toy, the child must get permission from a parent and permission from the toy owner\.

[Bucket policies and user policies](using-iam-policies.md)

### Permission delegation<a name="permission-delegation"></a>

If an AWS account owns a resource, it can grant those permissions to another AWS account\. That account can then delegate those permissions, or a subset of them, to users in the account\. This is referred to as permission delegation\. But an account that receives permissions from another account cannot delegate permission cross\-account to another AWS account\. 

## Related topics<a name="access-control-guidelines-related-topics"></a>

We recommend you first review all introductory topics that explain how you manage access to your Amazon S3 resources and related guidelines\. For more information, see [Identity and access management in Amazon S3](s3-access-control.md)\. You can then use the following topics for more information about specific access policy options\. 
+ [Access control list \(ACL\) overview](acl-overview.md)
+ [Controlling ownership of uploaded objects using S3 Object Ownership](about-object-ownership.md)