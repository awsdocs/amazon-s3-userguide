# Identity and access management in Amazon S3<a name="s3-access-control"></a>

By default, all Amazon S3 resources—buckets, objects, and related subresources \(for example, `lifecycle` configuration and `website` configuration\)—are private\. Only the resource owner, the AWS account that created it, can access the resource\. The resource owner can optionally grant access permissions to others by writing an access policy\. 

Amazon S3 offers access policy options broadly categorized as resource\-based policies and user policies\. Access policies that you attach to your resources \(buckets and objects\) are referred to as *resource\-based policies*\. For example, bucket policies and access point policies are resource\-based policies\. You can also attach access policies to users in your account\. These are called *user policies*\. You can choose to use resource\-based policies, user policies, or some combination of these to manage permissions to your Amazon S3 resources\. You can also use access control lists \(ACLs\) to grant basic read and write permissions to other AWS accounts\. 

By default, when another AWS account uploads an object to your S3 bucket, that account \(the object writer\) owns the object, has access to it, and can grant other users access to it through ACLs\. You can use Object Ownership to change this default behavior so that ACLs are disabled and you, as the bucket owner, automatically own every object in your bucket\. As a result, access control for your data is based on policies, such as IAM policies, S3 bucket policies, virtual private cloud \(VPC\) endpoint policies, and AWS Organizations service control policies \(SCPs\)\.

A majority of modern use cases in Amazon S3 no longer require the use of ACLs, and we recommend that you disable ACLs except in unusual circumstances where you need to control access for each object individually\. With Object Ownership, you can disable ACLs and rely on policies for access control\. When you disable ACLs, you can easily maintain a bucket with objects uploaded by different AWS accounts\. You, as the bucket owner, own all the objects in the bucket and can manage access to them using policies\. For more information, see [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\.

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
+ [Reviewing bucket access using Access Analyzer for S3](access-analyzer.md)
+ [Verifying bucket ownership with bucket owner condition](bucket-owner-condition.md)