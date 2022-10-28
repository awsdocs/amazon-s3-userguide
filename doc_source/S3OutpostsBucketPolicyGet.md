# Viewing the bucket policy for your Amazon S3 on Outposts bucket<a name="S3OutpostsBucketPolicyGet"></a>

A bucket policy is a resource\-based AWS Identity and Access Management \(IAM\) policy that you can use to grant access permissions to your bucket and the objects in it\. Only the bucket owner can associate a policy with a bucket\. The permissions attached to the bucket apply to all of the objects in the bucket that are owned by the bucket owner\. Bucket policies are limited to 20 KB in size\. For more information, see [Bucket policy](S3onOutposts.md#S3OutpostsBucketPolicies)\.

The following topics show you how to view your Amazon S3 on Outposts bucket policy by using the AWS Management Console, AWS Command Line Interface \(AWS CLI\), or AWS SDK for Java\. 

## Using the S3 console<a name="s3-outposts-bucket-policy"></a>

**To create or edit a bucket policy**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Outposts buckets**\.

1. Choose the Outposts bucket whose permission you want to edit\.

1. Choose the **Permissions** tab\.

1. In the **Outposts bucket policy** section, you can review your existing bucket policy\. For more information, see [Setting up IAM with S3 on Outposts](S3OutpostsIAM.md)\.

## Using the AWS CLI<a name="S3OutpostsGetBucketPolicyCLI"></a>

The following AWS CLI example gets a policy for an Outposts bucket\. To run this command, replace the `user input placeholders` with your own information\.

```
aws s3control get-bucket-policy --account-id 123456789012 --bucket arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/bucket/example-outposts-bucket
```

## Using the AWS SDK for Java<a name="S3OutpostsGetBucketPolicyJava"></a>

The following SDK for Java example gets a policy for an Outposts bucket\.

```
import com.amazonaws.services.s3control.model.*;

public void getBucketPolicy(String bucketArn) {

    GetBucketPolicyRequest reqGetBucketPolicy = new GetBucketPolicyRequest()
            .withAccountId(AccountId)
            .withBucket(bucketArn);

    GetBucketPolicyResult respGetBucketPolicy = s3ControlClient.getBucketPolicy(reqGetBucketPolicy);
    System.out.printf("GetBucketPolicy Response: %s%n", respGetBucketPolicy.toString());

}
```