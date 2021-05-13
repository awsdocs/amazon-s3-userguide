# Viewing and editing properties of Amazon S3 on Outposts buckets using the console<a name="s3-outposts-edit-bucket-properties"></a>

With Amazon S3 on Outposts, you can create S3 buckets on your AWS Outposts and easily store and retrieve objects on premises for applications that require local data access, local data processing, and data residency\. S3 on Outposts provides a new storage class, `OUTPOSTS`, which uses the S3 APIs, and is designed to store data durably and redundantly across multiple devices and servers on your AWS Outposts\. You communicate with your Outposts bucket using an access point and endpoint connection over a virtual private cloud \(VPC\)\. You can use the same APIs and features on Outposts buckets as you do on Amazon S3, including access policies, encryption, and tagging\. You can use S3 on Outposts through the AWS Management Console, AWS CLI, AWS SDKs, or REST API\. For more information, see [Using Amazon S3 on Outposts](S3onOutposts.md)\.

**Note**  
The AWS account that creates the bucket owns it and is the only one that can commit actions to it\. Buckets have configuration properties such as Outpost, tags, default encryption, and access point settings\. Access point settings include the VPC and access point policy for accessing the objects in the bucket, and other metadata\. For more information, see [S3 on Outposts specifications](S3OnOutpostsRestrictionsLimitations.md#S3OnOutpostsSpecifications)\.  
The Amazon S3 console doesn’t support S3 on Outposts object actions\. For that you must use the REST API, AWS CLI, or AWS SDKs\.

As a bucket owner, you can change the following for your S3 on Outposts bucket:

**Topics**
+ [Adding and removing tags for S3 on Outposts buckets](s3-outposts-add-bucket-tags.md)
+ [Adding an S3 on Outposts bucket to CloudTrail](s3-outposts-add-bucket-events-cloudtrail.md)
+ [Managing S3 on Outposts bucket permissions](s3-outposts-bucket-edit-permissions.md)
+ [Managing an S3 on Outposts bucket's lifecycle rules](s3-outposts-bucket-lifecycle.md)
+ [Managing S3 on Outposts bucket access points](s3-outposts-bucket-edit-outpost-access-point.md)