# Emptying a bucket<a name="empty-bucket"></a>

You can empty a bucket's contents using the Amazon S3 console, AWS SDKs, or AWS Command Line Interface \(AWS CLI\)\. When you empty a bucket, you delete all the objects, but you keep the bucket\. After you empty a bucket, it cannot be undone\. When you empty a bucket that has S3 Bucket Versioning enabled or suspended, all versions of all the objects in the bucket are deleted\. For more information, see [Working with objects in a versioning\-enabled bucket](manage-objects-versioned-bucket.md)\.

You can also specify a lifecycle configuration on a bucket to expire objects so that Amazon S3 can delete them\. For more information, see [Setting lifecycle configuration on a bucket](how-to-set-lifecycle-configuration-intro.md)

**Troubleshooting**  
Objects added to the bucket while the empty bucket action is in progress might be deleted\. To prevent new objects from being added to a bucket while the empty bucket action is in progress, you might need to stop your AWS CloudTrail trails from logging events to the bucket\. For more information, see [Turning off logging for a trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-delete-trails-console.html) in the *AWS CloudTrail User Guide*\.

Another alternative to stopping CloudTrail trails from being added to the bucket is to add a deny s3:PutObject statement to your bucket policy\. If you want to store new objects in the bucket, you should remove the deny s3:PutObject statement from your bucket policy\. For more information, see [Example — Object operations](using-with-s3-actions.md#using-with-s3-actions-related-to-objects) and [IAM JSON policy elements: Effect](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_effect.html) in the *IAM User Guide* 

## Using the S3 console<a name="empty-bucket-console"></a>

You can use the Amazon S3 console to empty a bucket, which deletes all of the objects in the bucket without deleting the bucket\. 

**To empty an S3 bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Bucket name** list, select the option next to the name of the bucket that you want to empty, and then choose **Empty**\.

1. On the **Empty bucket** page, confirm that you want to empty the bucket by entering the bucket name into the text field, and then choose **Empty**\.

1. Monitor the progress of the bucket emptying process on the **Empty bucket: Status** page\.

## Using the AWS CLI<a name="empty-bucket-awscli"></a>

You can empty a bucket using the AWS CLI only if the bucket does not have Bucket Versioning enabled\. If versioning is not enabled, you can use the `rm` \(remove\) AWS CLI command with the `--recursive` parameter to empty the bucket \(or remove a subset of objects with a specific key name prefix\)\. 

The following `rm` command removes objects that have the key name prefix `doc`, for example, `doc/doc1` and `doc/doc2`\.

```
$ aws s3 rm s3://bucket-name/doc --recursive
```

Use the following command to remove all objects without specifying a prefix\.

```
$ aws s3 rm s3://bucket-name --recursive
```

For more information, see [Using high\-level S3 commands with the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/using-s3-commands.html) in the *AWS Command Line Interface User Guide*\.

**Note**  
You can't remove objects from a bucket that has versioning enabled\. Amazon S3 adds a delete marker when you delete an object, which is what this command does\. For more information about S3 Bucket Versioning, see [Using versioning in S3 buckets](Versioning.md)\.

## Using the AWS SDKs<a name="empty-bucket-awssdks"></a>

You can use the AWS SDKs to empty a bucket or remove a subset of objects that have a specific key name prefix\.

For an example of how to empty a bucket using AWS SDK for Java, see [Deleting a bucket](delete-bucket.md)\. The code deletes all objects, regardless of whether the bucket has versioning enabled, and then it deletes the bucket\. To just empty the bucket, make sure that you remove the statement that deletes the bucket\. 

For more information about using other AWS SDKs, see [Tools for Amazon Web Services](https://aws.amazon.com/tools/)\.

## Using a lifecycle configuration<a name="empty-bucket-lifecycle"></a>

If you use a lifecycle policy to empty your bucket, the lifecycle policy should include [current versions, non\-current versions](https://docs.aws.amazon.com/AmazonS3/latest/userguide/versioning-workflows.html), [delete markers](https://docs.aws.amazon.com/AmazonS3/latest/userguide/DeleteMarker.html), and [incomplete multipart uploads](https://docs.aws.amazon.com/AmazonS3/latest/userguide/mpu-abort-incomplete-mpu-lifecycle-config.html)\.

You can add lifecycle configuration rules to expire all objects or a subset of objects that have a specific key name prefix\. For example, to remove all objects in a bucket, you can set a lifecycle rule to expire objects one day after creation\.

Amazon S3 supports a bucket lifecycle rule that you can use to stop multipart uploads that don't complete within a specified number of days after being initiated\. We recommend that you configure this lifecycle rule to minimize your storage costs\. For more information, see [Configuring a bucket lifecycle policy to abort incomplete multipart uploads](mpu-abort-incomplete-mpu-lifecycle-config.md)\.

For more information about using a lifecycle configuration to empty a bucket, see [Setting lifecycle configuration on a bucket](how-to-set-lifecycle-configuration-intro.md) and [Expiring objects](lifecycle-expire-general-considerations.md)\.