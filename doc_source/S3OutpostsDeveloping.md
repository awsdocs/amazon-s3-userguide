# Developing with S3 on Outposts<a name="S3OutpostsDeveloping"></a>

With Amazon S3 on Outposts, you can create S3 buckets on your AWS Outposts and easily store and retrieve objects on premises for applications that require local data access, local data processing, and data residency\. S3 on Outposts provides a new storage class, S3 Outposts \(`OUTPOSTS`\), which uses the Amazon S3 APIs, and is designed to store data durably and redundantly across multiple devices and servers on your AWS Outposts\. You communicate with your Outpost bucket by using an access point and endpoint connection over a virtual private cloud \(VPC\)\. You can use the same APIs and features on Outpost buckets as you do on Amazon S3 buckets, including access policies, encryption, and tagging\. You can use S3 on Outposts through the AWS Management Console, AWS Command Line Interface \(AWS CLI\), AWS SDKs, or REST API\. For more information, see [What is Amazon S3 on Outposts?](S3onOutposts.md)\.

The following topics provide information about developing with S3 on Outposts

**Topics**
+ [Amazon S3 on Outposts API operations](S3OutpostsAPI.md)
+ [Configure the S3 control client for S3 on Outposts using AWS SDK for Java](S3OutpostsCongfigureS3ControlClientJava.md)