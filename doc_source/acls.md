# Managing access with ACLs<a name="acls"></a>

 Access control lists \(ACLs\) are one of the resource\-based options \(see [Overview of managing access](access-control-overview.md)\) that you can use to manage access to your buckets and objects\. You can use ACLs to grant basic read/write permissions to other AWS accounts\. There are limits to managing permissions using ACLs\.

For example, you can grant permissions only to other AWS accounts; you cannot grant permissions to users in your account\. You cannot grant conditional permissions, nor can you explicitly deny permissions\. ACLs are suitable for specific scenarios\. For example, if a bucket owner allows other AWS accounts to upload objects, permissions to those objects can only be managed using object ACL by the AWS account that owns the object\.

By default, when another AWS account uploads an object to your S3 bucket, that account \(the object writer\) owns the object, has access to it, and can grant other users access to it through ACLs\. You can use Object Ownership to change this default behavior so that ACLs are disabled and you, as the bucket owner, automatically own every object in your bucket\. As a result, access control for your data is based on policies, such as IAM policies, S3 bucket policies, virtual private cloud \(VPC\) endpoint policies, and AWS Organizations service control policies \(SCPs\)\.

A majority of modern use cases in Amazon S3 no longer require the use of ACLs, and we recommend that you disable ACLs except in unusual circumstances where you need to control access for each object individually\. With Object Ownership, you can disable ACLs and rely on policies for access control\. When you disable ACLs, you can easily maintain a bucket with objects uploaded by different AWS accounts\. You, as the bucket owner, own all the objects in the bucket and can manage access to them using policies\. For more information, see [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\.

**Important**  
If your bucket uses the bucket owner enforced setting for S3 Object Ownership, you must use policies to grant access to your bucket and the objects in it\. Requests to set ACLs or update ACLs fail and return the `AccessControlListNotSupported` error code\. Requests to read ACLs are still supported\.

For more information about ACLs, see the following topics\.

**Topics**
+ [Access control list \(ACL\) overview](acl-overview.md)
+ [Finding the canonical user ID for your AWS account](finding-canonical-user-id.md)
+ [Configuring ACLs](managing-acls.md)
