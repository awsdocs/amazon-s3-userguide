# Managing access with ACLs<a name="acls"></a>

 Access control lists \(ACLs\) are one of the resource\-based access policy options \(see [Overview of managing access](access-control-overview.md)\) that you can use to manage access to your buckets and objects\. You can use ACLs to grant basic read/write permissions to other AWS accounts\. There are limits to managing permissions using ACLs\.

For example, you can grant permissions only to other AWS accounts; you cannot grant permissions to users in your account\. You cannot grant conditional permissions, nor can you explicitly deny permissions\. ACLs are suitable for specific scenarios\. For example, if a bucket owner allows other AWS accounts to upload objects, permissions to these objects can only be managed using object ACL by the AWS account that owns the object\.

For more information about access policy options, see [Access policy guidelines](access-policy-alternatives-guidelines.md)\. For more information about ACLs, see the following topics\.

**Topics**
+ [Access control list \(ACL\) overview](acl-overview.md)
+ [Finding the canonical user ID for your AWS account](finding-canonical-user-id.md)
+ [Configuring ACLs](managing-acls.md)