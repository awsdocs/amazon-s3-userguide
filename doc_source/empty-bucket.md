--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Emptying a bucket<a name="empty-bucket"></a>

You can empty a bucket's contents using the Amazon S3 console, AWS SDKs, or AWS Command Line Interface \(AWS CLI\)\. When you empty a bucket, you delete all the content, but you keep the bucket\. 

You can also specify a lifecycle configuration on a bucket to expire objects so that Amazon S3 can delete them\. However, there are limitations on this method based on the number of objects in your bucket and the bucket's versioning status\.

## Using the S3 console<a name="empty-bucket-console"></a>

You can use the Amazon S3 console to empty a bucket, which deletes all of the objects in the bucket without deleting the bucket\. When you empty a bucket that has S3 Bucket Versioning enabled, all versions of all the objects in the bucket are deleted\. For more information, see [Working with objects in a versioning\-enabled bucket](manage-objects-versioned-bucket.md)\.

**To empty an S3 bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Bucket name** list, select the option next to the name of the bucket that you want to empty, and then choose **Empty**\.

1. On the **Empty bucket** page, confirm that you want to empty the bucket by entering the bucket name into the text field, and then choose **Empty**\.

1. \(Optional\) Monitor the progress of the bucket emptying process on the **Empty bucket: Status** page\.

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

You can configure lifecycle on your bucket to expire objects and request that Amazon S3 delete expired objects\. You can add lifecycle configuration rules to expire all or a subset of objects that have a specific key name prefix\. For example, to remove all objects in a bucket, you can set a lifecycle rule to expire objects one day after creation\.

If your bucket has versioning enabled, you can also configure the rule to expire noncurrent objects\. To fully empty the contents of a versioning\-enabled bucket, you must configure an expiration policy on both current and noncurrent objects in the bucket\.

For more information, see [Managing your storage lifecycle](object-lifecycle-mgmt.md) and [Expiring objects](lifecycle-expire-general-considerations.md)\.