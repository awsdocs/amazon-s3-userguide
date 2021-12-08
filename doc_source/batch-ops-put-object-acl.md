# Replace access control list<a name="batch-ops-put-object-acl"></a>

The **Replace access control list \(ACL\)** operation replaces the Amazon S3 access control lists \(ACLs\) for every object that is listed in the manifest\. Using ACLs, you can define who can access an object and what actions they can perform\.

S3 Batch Operations support custom ACLs that you define and canned ACLs that Amazon S3 provides with a predefined set of access permissions\.

If the objects in your manifest are in a versioned bucket, you can apply the ACLs to specific versions of every object\. You do this by specifying a version ID for every object in the manifest\. If you don't include a version ID for any object, then S3 Batch Operations applies the ACL to the latest version of the object\.

For more information about ACLs in Amazon S3, [Access control list \(ACL\) overview](acl-overview.md)\.

**S3 Block Public Access**  
If you want to limit public access to all objects in a bucket, you should use Amazon S3 Block Public Access instead of S3 Batch Operations\. Block Public Access can limit public access on a per\-bucket or account\-wide basis with a single, simple operation that takes effect quickly\. This makes it a better choice when your goal is to control public access to all objects in a bucket or account\. Use S3 Batch Operations when you need to apply a customized ACL to every object in the manifest\. For more information about S3 Block Public Access, see [Blocking public access to your Amazon S3 storage](access-control-block-public-access.md)\.

**S3 Object Ownership**  
If the objects in the manifest are in a bucket uses the bucket owner enforced setting for Object Ownership, the **Replace access control list \(ACL\)** operation can only specify object ACLs that grant full control to the bucket owner\. The operation can't grant object ACL permissions to other AWS accounts or groups\. For more information, see [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\.

## Restrictions and limitations<a name="batch-ops-put-object-acl-restrictions"></a>
+ The role that you specify to run the Replace access control list job must have permissions to perform the underlying Amazon S3 `PutObjectAcl` operation\. For more information about the permissions required, see [PutObjectAcl](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectAcl.html) in the *Amazon Simple Storage Service API Reference*\.
+ S3 Batch Operations uses the Amazon S3 `PutObjectAcl` operation to apply the specified ACL to every object in the manifest\. Therefore, all restrictions and limitations that apply to the underlying PutObjectAcl operation also apply to S3 Batch Operations Replace access control list jobs\.