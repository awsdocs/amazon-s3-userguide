# Using bucket policies<a name="bucket-policies"></a>

You can create and configure bucket policies to grant permission to your Amazon S3 resources\.

A bucket policy is a resource\-based policy that you can use to grant access permissions to your bucket and the objects in it\. Only the bucket owner can associate a policy with a bucket\. The permissions attached to the bucket apply to all of the objects in the bucket that are owned by the bucket owner\. These permissions do not apply to objects owned by other AWS accounts\.

By default, when another AWS account uploads an object to your S3 bucket, that account \(the object writer\) owns the object, has access to it, and can grant other users access to it through access control lists \(ACLs\)\. You can use Object Ownership to change this default behavior so that ACLs are disabled and you, as the bucket owner, automatically own every object in your bucket\. As a result, access control for your data is based on policies, such as IAM policies, S3 bucket policies, virtual private cloud \(VPC\) endpoint policies, and AWS Organizations service control policies \(SCPs\)\. For more information, see [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\.

Bucket policies use JSON\-based access policy language\. You can use bucket policies to add or deny permissions for the objects in a bucket\. Bucket policies allow or deny requests based on the elements in the policy, including the requester, S3 actions, resources, and aspects or conditions of the request \(for example, the IP address used to make the request\)\. For example, you can create a bucket policy that grants cross\-account permissions to upload objects to an S3 bucket while ensuring that the bucket owner has full control of the uploaded objects\. For more information, see [Bucket policy examples](example-bucket-policies.md)\.

In your bucket policy, you can use wildcard characters on Amazon Resource Names \(ARNs\) and other values to grant permissions to a subset of objects\. For example, you can control access to groups of objects that begin with a common [prefix](https://docs.aws.amazon.com/general/latest/gr/glos-chap.html#keyprefix) or end with a given extension, such as `.html`\.

The topics in this section provide examples and show you how to add a bucket policy in the S3 console\. For information about IAM user policies, see [Using IAM user policies](user-policies.md)\. For information about bucket policy language, see [Policies and Permissions in Amazon S3](access-policy-language-overview.md)

**Important**  
Bucket policies are limited to 20 KB in size\.

**Topics**
+ [Adding a bucket policy using the Amazon S3 console](add-bucket-policy.md)
+ [Controlling access from VPC endpoints with bucket policies](example-bucket-policies-vpc-endpoint.md)
+ [Bucket policy examples](example-bucket-policies.md)