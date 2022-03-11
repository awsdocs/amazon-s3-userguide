# Deleting your Amazon S3 on Outposts bucket<a name="S3OutpostsDeleteBucket"></a>

With Amazon S3 on Outposts, you can create S3 buckets on your AWS Outposts and easily store and retrieve objects on premises for applications that require local data access, local data processing, and data residency\. S3 on Outposts provides a new storage class, S3 Outposts \(`OUTPOSTS`\), which uses the S3 APIs, and is designed to store data durably and redundantly across multiple devices and servers on your AWS Outposts\. You communicate with your Outpost bucket by using an access point and endpoint connection over a virtual private cloud \(VPC\)\. You can use the same APIs and features on Outpost buckets as you do on Amazon S3 buckets, including access policies, encryption, and tagging\. You can use S3 on Outposts through the AWS Management Console, AWS CLI, AWS SDKs, or REST API\. For more information, see [What is Amazon S3 on Outposts?](S3onOutposts.md)

For more information about working with buckets in S3 on Outposts, see [Working with S3 on Outposts buckets](S3OutpostsWorkingBuckets.md)\.

The AWS account that creates the bucket owns it and is the only one that can delete it\.

**Note**  
Outposts buckets must be empty before they can be deleted\.
The Amazon S3 console doesn’t support S3 on Outposts object actions\. For that you must use the REST API, AWS CLI, or AWS SDKs\.  
Deleting an Outposts bucket will fail if the bucket has Outposts access points\.  
You cannot recover a bucket after it has been deleted\.

The following examples show you how to delete an S3 on Outposts bucket using the AWS Management Console and AWS Command Line Interface \(AWS CLI\)\.

## Using the S3 console<a name="s3-outposts-delete-bucket"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Outposts buckets**\.

1. Choose the bucket that you want to delete, and choose **Delete**\.

1. Confirm the deletion\.

## Using the AWS CLI<a name="s3-outposts-delete-bucket-cli"></a>

The following example deletes an S3 on Outposts bucket \(`s3-outposts:DeleteBucket`\) using the AWS CLI\. 

```
aws s3control delete-bucket --account-id 123456789012 --bucket arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/bucket/example-outpost-bucket
```