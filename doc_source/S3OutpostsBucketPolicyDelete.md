# Deleting the bucket policy for your Amazon S3 on Outposts bucket<a name="S3OutpostsBucketPolicyDelete"></a>

A bucket policy is a resource\-based AWS Identity and Access Management \(IAM\) policy that you can use to grant access permissions to your bucket and the objects in it\. Only the bucket owner can associate a policy with a bucket\. The permissions attached to the bucket apply to all of the objects in the bucket that are owned by the bucket owner\. Bucket policies are limited to 20 KB in size\. For more information, see [Bucket policy](S3onOutposts.md#S3OutpostsBucketPolicies)\.

The following topics show you how to view your Amazon S3 on Outposts bucket policy using the AWS Management Console or AWS Command Line Interface \(AWS CLI\)\. 

## Using the S3 console<a name="s3-outposts-bucket-delete-policy"></a>

**To delete a bucket policy**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Outposts buckets**\.

1. Choose the Outposts bucket whose permission you want to edit\.

1. Choose the **Permissions** tab\.

1. In the **Outposts bucket policy** section, choose **Delete**\.

1. Confirm the deletion\.

## Using the AWS CLI<a name="S3OutpostsBucketPolicyDeleteCLI"></a>

The following example deletes the bucket policy for an S3 on Outposts bucket \(`s3-outposts:DeleteBucket`\) using the AWS CLI\. 

```
aws s3control delete-bucket-policy --account-id 123456789012 --bucket arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/bucket/example-outpost-bucket
```