# Creating and managing a lifecycle configuration for your Amazon S3 on Outposts bucket<a name="S3OutpostsLifecycleManaging"></a>

Lifecycle rules for S3 on Outposts buckets are limited to object deletion\. You can use lifecycle rules to define when to initiate object deletion based on age or date\. You can create, enable, disable, or delete a lifecycle rule\.

**Note**  
The AWS account that creates the bucket owns it and is the only one that can create, enable, disable, or delete a lifecycle rule\.

**Note**  
The AWS account that creates the bucket owns it and is the only one that can commit actions to it\. Buckets have configuration properties, such as Outpost, tag, default encryption, and access point settings\. Access point settings include the virtual private cloud \(VPC\), the access point policy for accessing the objects in the bucket, and other metadata\. For more information, see [S3 on Outposts specifications](S3OnOutpostsRestrictionsLimitations.md#S3OnOutpostsSpecifications)\.

To create and manage the lifecycle configuration for your S3 on Outposts bucket, see the following topics\.

**Topics**
+ [Creating and managing a lifecycle rule using the AWS Management Console](S3OutpostsLifecycleConsole.md)
+ [Creating and managing a lifecycle configuration using the AWS CLI and SDK for Java](S3OutpostsLifecycleCLIJava.md)