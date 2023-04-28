# Using bucket policies<a name="bucket-policies"></a>

A bucket policy is a resource\-based policy that you can use to grant access permissions to your Amazon S3 bucket and the objects in it\. Only the bucket owner can associate a policy with a bucket\. The permissions attached to the bucket apply to all of the objects in the bucket that are owned by the bucket owner\. These permissions do not apply to objects that are owned by other AWS accounts\.

By default, when another AWS account uploads an object to your S3 bucket, that account \(the object writer\) owns the object, has access to it, and can grant other users access to it through access control lists \(ACLs\)\. You can use Object Ownership to change this default behavior so that ACLs are disabled and you, as the bucket owner, automatically own every object in your bucket\. As a result, access control for your data is based on policies, such as IAM user policies, S3 bucket policies, virtual private cloud \(VPC\) endpoint policies, and AWS Organizations service control policies \(SCPs\)\. For more information, see [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\.

Bucket policies use JSON\-based IAM policy language\. You can use bucket policies to add or deny permissions for the objects in a bucket\. Bucket policies can allow or deny requests based on the elements in the policy\. These elements include the requester, S3 actions, resources, and aspects or conditions of the request \(such as the IP address that's used to make the request\)\. 

For example, you can create a bucket policy that does the following: 
+ Grants other accounts cross\-account permissions to upload objects to your S3 bucket
+ Makes sure that you, the bucket owner, has full control of the uploaded objects

For more information, see [Bucket policy examples](example-bucket-policies.md)\.

The topics in this section provide examples and show you how to add a bucket policy in the S3 console\. For information about IAM user policies, see [Using IAM user policies](user-policies.md)\. For information about bucket policy language, see [Policies and Permissions in Amazon S3](access-policy-language-overview.md)\.

**Topics**
+ [Adding a bucket policy by using the Amazon S3 console](add-bucket-policy.md)
+ [Controlling access from VPC endpoints with bucket policies](example-bucket-policies-vpc-endpoint.md)
+ [Bucket policy examples](example-bucket-policies.md)