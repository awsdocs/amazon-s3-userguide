# Using bucket policies<a name="bucket-policies"></a>

A bucket policy is a resource\-based policy that you can use to grant access permissions to your Amazon S3 bucket and the objects in it\. Only the bucket owner can associate a policy with a bucket\. The permissions attached to the bucket apply to all of the objects in the bucket that are owned by the bucket owner\. These permissions do not apply to objects that are owned by other AWS accounts\.

 S3 Object Ownership is an Amazon S3 bucket\-level setting that you can use to control ownership of objects uploaded to your bucket and to disable or enable ACLs\. By default, Object Ownership is set to the Bucket owner enforced setting and all ACLs are disabled\. The bucket owner owns all the objects in the bucket and manages access to data exclusively using policies\.

Bucket policies use JSON\-based IAM policy language\. You can use bucket policies to add or deny permissions for the objects in a bucket\. Bucket policies can allow or deny requests based on the elements in the policy\. These elements include the requester, S3 actions, resources, and aspects or conditions of the request \(such as the IP address that's used to make the request\)\. 

For example, you can create a bucket policy that does the following: 
+ Grants other accounts cross\-account permissions to upload objects to your S3 bucket
+ Makes sure that you, the bucket owner, has full control of the uploaded objects

For more information, see [Bucket policy examples](example-bucket-policies.md)\.

The topics in this section provide examples and show you how to add a bucket policy in the S3 console\. For information about IAM user policies, see [Using IAM user and role policies](user-policies.md)\. For information about bucket policy language, see [Policies and Permissions in Amazon S3](access-policy-language-overview.md)\.

**Topics**
+ [Adding a bucket policy by using the Amazon S3 console](add-bucket-policy.md)
+ [Controlling access from VPC endpoints with bucket policies](example-bucket-policies-vpc-endpoint.md)
+ [Bucket policy examples](example-bucket-policies.md)