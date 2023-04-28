# Identity and access management in Amazon S3<a name="s3-access-control"></a>

By default, all Amazon S3 resources—buckets, objects, and related subresources \(for example, `lifecycle` configuration and `website` configuration\)—are private\. Only the resource owner, the AWS account that created it, can access the resource\. The resource owner can optionally grant access permissions to others by writing an access policy\. 

Amazon S3 offers access policy options broadly categorized as resource\-based policies and user policies\. Access policies that you attach to your resources \(buckets and objects\) are referred to as *resource\-based policies*\. For example, bucket policies and access point policies are resource\-based policies\. You can also attach access policies to users in your account\. These are called *user policies*\. You can choose to use resource\-based policies, user policies, or some combination of these to manage permissions to your Amazon S3 resources\. You can also use access control lists \(ACLs\) to grant basic read and write permissions to other AWS accounts\. 

S3 Object Ownership is an Amazon S3 bucket\-level setting that you can use to both control ownership of the objects that are uploaded to your bucket and to disable or enable ACLs\. By default, Object Ownership is set to the bucket owner enforced setting, and all ACLs are disabled\. When ACLs are disabled, the bucket owner owns all the objects in the bucket and manages access to them exclusively by using access\-management policies\.

 A majority of modern use cases in Amazon S3 no longer require the use of ACLs\. We recommend that you keep ACLs disabled, except in unusual circumstances where you need to control access for each object individually\. With ACLs disabled, you can use policies to control access to all objects in your bucket, regardless of who uploaded the objects to your bucket\. For more information, see [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\.

For more information about managing access to your Amazon S3 objects and buckets, see the topics below\.

**Topics**
+ [Overview of managing access](access-control-overview.md)
+ [Access policy guidelines](access-policy-alternatives-guidelines.md)
+ [How Amazon S3 authorizes a request](how-s3-evaluates-access-control.md)
+ [Bucket policies and user policies](using-iam-policies.md)
+ [AWS managed policies for Amazon S3](security-iam-awsmanpol.md)
+ [Managing access with ACLs](acls.md)
+ [Using cross\-origin resource sharing \(CORS\)](cors.md)
+ [Blocking public access to your Amazon S3 storage](access-control-block-public-access.md)
+ [Reviewing bucket access using IAM Access Analyzer for S3](access-analyzer.md)
+ [Verifying bucket ownership with bucket owner condition](bucket-owner-condition.md)