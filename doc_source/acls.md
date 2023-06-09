# Managing access with ACLs<a name="acls"></a>

 Access control lists \(ACLs\) are one of the resource\-based options \(see [Overview of managing access](access-control-overview.md)\) that you can use to manage access to your buckets and objects\. You can use ACLs to grant basic read/write permissions to other AWS accounts\. There are limits to managing permissions using ACLs\.

For example, you can grant permissions only to other AWS accounts; you cannot grant permissions to users in your account\. You cannot grant conditional permissions, nor can you explicitly deny permissions\. ACLs are suitable for specific scenarios\. For example, if a bucket owner allows other AWS accounts to upload objects, permissions to these objects can only be managed using object ACL by the AWS account that owns the object\.

S3 Object Ownership is an Amazon S3 bucket\-level setting that you can use to both control ownership of the objects that are uploaded to your bucket and to disable or enable ACLs\. By default, Object Ownership is set to the bucket owner enforced setting, and all ACLs are disabled\. When ACLs are disabled, the bucket owner owns all the objects in the bucket and manages access to them exclusively by using access\-management policies\.

 A majority of modern use cases in Amazon S3 no longer require the use of ACLs\. We recommend that you keep ACLs disabled, except in unusual circumstances where you need to control access for each object individually\. With ACLs disabled, you can use policies to control access to all objects in your bucket, regardless of who uploaded the objects to your bucket\. For more information, see [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\.

**Important**  
If your bucket uses the bucket owner enforced setting for S3 Object Ownership, you must use policies to grant access to your bucket and the objects in it\. With the bucket owner enforced setting enabled, requests to set access control lists \(ACLs\) or update ACLs fail and return the `AccessControlListNotSupported` error code\. Requests to read ACLs are still supported\.

For more information about ACLs, see the following topics\.

**Topics**
+ [Access control list \(ACL\) overview](acl-overview.md)