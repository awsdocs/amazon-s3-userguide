# Access control best practices<a name="access-control-best-practices"></a>

Amazon S3 provides a variety of security features and tools\. The following scenarios should serve as a guide to what tools and settings you might want to use when performing certain tasks or operating in specific environments\. Proper application of these tools can help maintain the integrity of your data and help ensure that your resources are accessible to the intended users\. 

**Topics**
+ [Creating a new bucket](#access-control-best-practices-new-bucket)
+ [Storing and sharing data](#access-control-best-practices-store-share)
+ [Sharing resources](#access-control-best-practices-groups)
+ [Protecting data](#access-control-best-practices-groups)

## Creating a new bucket<a name="access-control-best-practices-new-bucket"></a>

When creating a new bucket, you should apply the following tools and settings to help ensure that your Amazon S3 resources are protected\.  

**Block Public Access**  
S3 Block Public Access provides four settings to help you avoid inadvertently exposing your S3 resources\. You can apply these settings in any combination to individual access points, buckets, or entire AWS accounts\. If you apply a setting to an account, it applies to all buckets and access points that are owned by that account\. By default, the **Block all public access** setting is applied to new buckets created in the Amazon S3 console\. 

For more information, see [The meaning of "public"](access-control-block-public-access.md#access-control-block-public-access-policy-status)\.

If the S3 Block Public Access settings are too restrictive, you can use AWS Identity and Access Management \(IAM\) identities to grant access to specific users rather than disabling all Block Public Access settings\. Using Block Public Access with IAM identities helps ensure that any operation that is blocked by a Block Public Access setting is rejected unless the requesting user has been given specific permission\. 

For more information, see [Block public access settings](access-control-block-public-access.md#access-control-block-public-access-options)\.

**Grant access with IAM identities**  
When setting up accounts for new team members who require S3 access, use IAM users and roles to ensure least privileges\. You can also implement a form of IAM multi\-factor authentication \(MFA\) to support a strong identity foundation\. Using IAM identities, you can grant unique permissions to users and specify what resources they can access and what actions they can take\. IAM identities provide increased capabilities, including the ability to require users to enter login credentials before accessing shared resources and apply permission hierarchies to different objects within a single bucket\. 

For more information, see [Example 1: Bucket owner granting its users bucket permissions ](example-walkthroughs-managing-access-example1.md)\.

**Bucket policies**  
With bucket policies, you can personalize bucket access to help ensure that only those users you have approved can access resources and perform actions within them\. In addition to bucket policies, you should use bucket\-level Block Public Access settings to further limit public access to your data\. 

For more information, see [Using bucket policies](bucket-policies.md)\.

When creating policies, avoid the use of wildcards in the `Principal` element because it effectively allows anyone to access your Amazon S3 resources\. It's better to explicitly list users or groups that are allowed to access the bucket\. Rather than including a wildcard for their actions, grant them specific permissions when applicable\. 

To further maintain the practice of least privileges, Deny statements in the `Effect` element should be as broad as possible and Allow statements should be as narrow as possible\. Deny effects paired with the "`s3:*`" action are another good way to implement opt\-in best practices for the users included in policy condition statements\.

For more information about specifying conditions for when a policy is in effect, see [Amazon S3 condition key examples](amazon-s3-policy-keys.md)\.

**Buckets in a VPC setting**  
When adding users in a corporate setting, you can use a virtual private cloud \(VPC\) endpoint to allow any users in your virtual network to access your Amazon S3 resources\. VPC endpoints enable developers to provide specific access and permissions to groups of users based on the network the user is connected to\. Rather than adding each user to an IAM role or group, you can use VPC endpoints to deny bucket access if the request doesn’t originate from the specified endpoint\.

For more information, see [Controlling access from VPC endpoints with bucket policies](example-bucket-policies-vpc-endpoint.md)\.

## Storing and sharing data<a name="access-control-best-practices-store-share"></a>

Use the following tools and best practices to store and share your Amazon S3 data\.

**Versioning and Object Lock for data integrity**  
If you use the Amazon S3 console to manage buckets and objects, you should implement S3 Versioning and S3 Object Lock\. These features help prevent accidental changes to critical data and enable you to roll back unintended actions\. This capability is particularly useful when there are multiple users with full write and execute permissions accessing the Amazon S3 console\. 

For information about S3 Versioning, see [Using versioning in S3 buckets](Versioning.md)\. For information about Object Lock, see [Using S3 Object Lock](object-lock.md)\.

**Object lifecycle management for cost efficiency**  
To manage your objects so that they are stored cost effectively throughout their lifecycle, you can pair lifecycle policies with object versioning\. Lifecycle policies define actions that you want S3 to take during an object's lifetime\. For example, you can create a lifecycle policy that will transition objects to another storage class, archive them, or delete them after a specified period of time\. You can define a lifecycle policy for all objects or a subset of objects in the bucket by using a shared prefix or tag\.

For more information, see [Managing your storage lifecycle](object-lifecycle-mgmt.md)\.

**Cross\-Region Replication for multiple office locations**  
When creating buckets that are accessed by different office locations, you should consider implementing S3 Cross\-Region Replication\. Cross\-Region Replication helps ensure that all users have access to the resources they need and increases operational efficiency\. Cross\-Region Replication offers increased availability by copying objects across S3 buckets in different AWS Regions\. However, the use of this tool increases storage costs\. 

For more information, see [Replicating objects](replication.md)\.

**Permissions for secure static website hosting**  
When configuring a bucket to be used as a publicly accessed static website, you need to disable all Block Public Access settings\. It is important to only provide `s3:GetObject` actions and not `ListObject` or `PutObject` permissions when writing the bucket policy for your static website\. This helps ensure that users cannot view all the objects in your bucket or add their own content\.

For more information, see [Setting permissions for website access](WebsiteAccessPermissionsReqd.md)\.

Amazon CloudFront provides the capabilities required to set up a secure static website\. Amazon S3 static websites only support HTTP endpoints\. CloudFront uses the durable storage of Amazon S3 while providing additional security headers like HTTPS\. HTTPS adds security by encrypting a normal HTTP request and protecting against common cyber attacks\.

For more information, see [Getting started with a secure static website](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/getting-started-secure-static-website-cloudformation-template.html) in the *Amazon CloudFront Developer Guide*\.

## Sharing resources<a name="access-control-best-practices-groups"></a>

There are several different ways that you can share resources with a specific group of users\. You can use the following tools to share a set of documents or other resources to a single group of users, department, or an office\. Although they can all be used to accomplish the same goal, some tools might pair better than others with your existing settings\. 

**User policies**  
You can share resources with a limited group of people using IAM groups and user policies\. When creating a new IAM user, you are prompted to create and add them to a group\. However, you can create and add users to groups at any point\. If the individuals you intend to share these resources with are already set up within IAM, you can add them to a common group and share the bucket with their group within the user policy\. You can also use IAM user policies to share individual objects within a bucket\.

For more information, see [Allowing an IAM user access to one of your buckets](example-policies-s3.md#iam-policy-ex0)\.



**Access control lists**  
As a general rule, we recommend that you use S3 bucket policies or IAM policies for access control\. Amazon S3 access control lists \(ACLs\) are a legacy access control mechanism that predates IAM\. If you already use S3 ACLs and you find them sufficient, there is no need to change\. However, certain access control scenarios require the use of ACLs\. For example, when a bucket owner wants to grant permission to objects, but not all objects are owned by the bucket owner, the object owner must first grant permission to the bucket owner\. This is done using an object ACL\.

For more information, see [Example 3: Bucket owner granting permissions to objects it does not own](example-walkthroughs-managing-access-example3.md)\.

**Prefixes**  
When trying to share specific resources from a bucket, you can replicate folder\-level permissions using prefixes\. The Amazon S3 console supports the folder concept as a means of grouping objects by using a shared name prefix for objects\. You can then specify a prefix within the conditions of an IAM user's policy to grant them explicit permission to access the resources associated with that prefix\. 

For more information, see [Organizing objects in the Amazon S3 console using folders](using-folders.md)\.

**Tagging**  
If you use object tagging to categorize storage, you can share objects that have been tagged with a specific value with specified users\. Resource tagging allows you to control access to objects based on the tags associated with the resource that a user is trying to access\. To do this, use the `ResourceTag/key-name` condition within an IAM user policy to allow access to the tagged resources\. 

For more information, see [Controlling access to AWS resources using resource tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_tags.html) in the *IAM User Guide*\.

## Protecting data<a name="access-control-best-practices-groups"></a>

Use the following tools to help protect data in transit and at rest, both of which are crucial in maintaining the integrity and accessibility of your data\.

**Object encryption**  
Amazon S3 offers several object encryption options that protect data in transit and at rest\. Server\-side encryption encrypts your object before saving it on disks in its data centers and then decrypts it when you download the objects\. As long as you authenticate your request and you have access permissions, there is no difference in the way you access encrypted or unencrypted objects\. When setting up server\-side encryption, you have three mutually exclusive options: 
+ Amazon S3 managed keys \(SSE\-S3\)
+ KMS keys stored in AWS Key Management Service \(SSE\-KMS\)
+ Customer\-provided keys \(SSE\-C\)

For more information, see [Protecting data using server\-side encryption](serv-side-encryption.md)\.

Client\-side encryption is the act of encrypting data before sending it to Amazon S3\. For more information, see [Protecting data using client\-side encryption](UsingClientSideEncryption.md)\.

**Signing methods**  
Signature Version 4 is the process of adding authentication information to AWS requests sent by HTTP\. For security, most requests to AWS must be signed with an access key, which consists of an access key ID and secret access key\. These two keys are commonly referred to as your security credentials\.

For more information, see [Authenticating Requests \(AWS Signature Version 4\)](https://docs.aws.amazon.com/AmazonS3/latest/API/sig-v4-authenticating-requests.html) and [Signature Version 4 signing process](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html)\.

**Logging and monitoring**  
Monitoring is an important part of maintaining the reliability, availability, and performance of your Amazon S3 solutions so that you can more easily debug a multi\-point failure if one occurs\. Logging can provide insight into any errors users are receiving, and when and what requests are made\. AWS provides several tools for monitoring your Amazon S3 resources: 
+ Amazon CloudWatch
+ AWS CloudTrail
+ Amazon S3 Access Logs
+ AWS Trusted Advisor

For more information, see [Logging and monitoring in Amazon S3](s3-incident-response.md)\.

Amazon S3 is integrated with AWS CloudTrail, a service that provides a record of actions taken by a user, a role, or an AWS service in Amazon S3\. This feature can be paired with Amazon GuardDuty, which monitors threats against your Amazon S3 resources by analyzing CloudTrail management events and CloudTrail S3 data events\. These data sources monitor different kinds of activity\. For example, S3 related CloudTrail management events include operations that list or configure S3 projects\. GuardDuty analyzes S3 data events from all of your S3 buckets and monitors them for malicious and suspicious activity\.

For more information, see [Amazon S3 protection in Amazon GuardDuty](https://docs.aws.amazon.com/guardduty/latest/ug/s3_detection.html) in the *Amazon GuardDuty User Guide*\.