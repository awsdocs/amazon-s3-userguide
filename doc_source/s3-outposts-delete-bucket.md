# Deleting an Amazon S3 on Outposts with Amazon S3 console<a name="s3-outposts-delete-bucket"></a>

With Amazon S3 on Outposts, you can create S3 buckets on your AWS Outposts and easily store and retrieve objects on\-premises for applications that require local data access, local data processing, and data residency\. S3 on Outposts provides a new storage class, `OUTPOSTS` which uses the S3 APIs, and is designed to durably and redundantly store data across multiple devices and servers on your AWS Outposts\. You communicate with your Outposts bucket using an access point and endpoint connection over a virtual private cloud \(VPC\)\. You can use the same APIs and features on Outposts buckets as you do on Amazon S3, such as access policies, encryption, and tagging\. You can use S3 on Outposts through the AWS Management Console, AWS CLI, AWS SDKs, or REST API\. For more information, see [Using Amazon S3 on Outposts](S3onOutposts.md)\.

The AWS account that creates the bucket owns it and is the only one that can delete it\.

**Note**  
Outposts buckets must be empty before they can be deleted\.
The Amazon S3 console doesn’t support S3 on Outposts object actions\. For that you will need to use the REST API, AWS CLI, or AWS SDKs\.  
Deleting an Outposts bucket will fail if this bucket has Outposts access points\.  
You cannot recover a bucket once it's deleted\.



**To delete an S3 on Outposts bucket:**

1. Sign in to the [Amazon S3 console](https://console.aws.amazon.com/s3)\.

1. Choose **Outposts buckets** from the left navigation menu\.

1. Choose the bucket that you want to delete\.

1. Choose **Delete**\.

   Confirm your deletion on the *delete Outposts bucket*page\.