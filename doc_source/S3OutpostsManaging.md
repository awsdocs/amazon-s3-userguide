# Managing S3 on Outposts storage<a name="S3OutpostsManaging"></a>

With Amazon S3 on Outposts, you can create S3 buckets on your AWS Outposts and easily store and retrieve objects on premises for applications that require local data access, local data processing, and data residency\. S3 on Outposts provides a new storage class, S3 Outposts \(`OUTPOSTS`\), which uses the Amazon S3 APIs, and is designed to store data durably and redundantly across multiple devices and servers on your AWS Outposts\. You communicate with your Outpost bucket by using an access point and endpoint connection over a virtual private cloud \(VPC\)\. You can use the same APIs and features on Outpost buckets as you do on Amazon S3 buckets, including access policies, encryption, and tagging\. You can use S3 on Outposts through the AWS Management Console, AWS Command Line Interface \(AWS CLI\), AWS SDKs, or REST API\. For more information, see [What is Amazon S3 on Outposts?](S3onOutposts.md)\.

For more information about managing, monitoring, and sharing your Amazon S3 on Outposts storage capacity, see the following topics\.

**Topics**
+ [Managing S3 on Outposts capacity with Amazon CloudWatch metrics](S3OutpostsCapacity.md)
+ [Receiving S3 on Outposts event notifications using Amazon CloudWatch Events](S3OutpostsNotificationsCWE.md)
+ [Managing S3 on Outposts capacity with AWS CloudTrail logs](S3OutpostsCloudtrail.md)
+ [Sharing Amazon S3 on Outposts using AWS Resource Access Manager](outposts-sharing-with-ram.md)
+ [Other AWS services that use S3 on Outposts](S3OutpostsOtherServices.md)