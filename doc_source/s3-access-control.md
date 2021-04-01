--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Identity and access management in Amazon S3<a name="s3-access-control"></a>

By default, all Amazon S3 resources—buckets, objects, and related subresources \(for example, `lifecycle` configuration and `website` configuration\)—are private\. Only the resource owner, the AWS account that created it, can access the resource\. The resource owner can optionally grant access permissions to others by writing an access policy\. 

Amazon S3 offers access policy options broadly categorized as resource\-based policies and user policies\. Access policies that you attach to your resources \(buckets and objects\) are referred to as *resource\-based policies*\. For example, bucket policies and access control lists \(ACLs\) are resource\-based policies\. You can also attach access policies to users in your account\. These are called *user policies*\. You can choose to use resource\-based policies, user policies, or some combination of these to manage permissions to your Amazon S3 resources\. The following sections provide general guidelines for managing permissions in Amazon S3\.

For more information about managing access to your Amazon S3 objects and buckets, see the topics below\.

**Topics**
+ [Overview of managing access](access-control-overview.md)
+ [Access policy guidelines](access-policy-alternatives-guidelines.md)
+ [How Amazon S3 authorizes a request](how-s3-evaluates-access-control.md)
+ [Bucket policies and user policies](using-iam-policies.md)
+ [Managing access with ACLs](acls.md)
+ [Using cross\-origin resource sharing \(CORS\)](cors.md)
+ [Blocking public access to your Amazon S3 storage](access-control-block-public-access.md)
+ [Managing data access with Amazon S3 access points](access-points.md)
+ [Reviewing bucket access using Access Analyzer for S3](access-analyzer.md)
+ [Controlling ownership of uploaded objects using S3 Object Ownership](about-object-ownership.md)
+ [Verifying bucket ownership with bucket owner condition](bucket-owner-condition.md)