# Setting up replication<a name="outposts-replication-how-setup"></a>

**Note**  
Objects that existed in your bucket before you set up a replication rule aren't replicated automatically\. In other words, Amazon S3 on Outposts doesn't replicate objects retroactively\. To replicate objects that were created before your replication configuration, you can use the `CopyObject` API operation to copy them to the same bucket\. After the objects are copied, they appear as "new" objects in the bucket and the replication configuration will apply to them\. For more information about copying an object, see [Copying an object in an Amazon S3 on Outposts bucket using the AWS SDK for Java](S3OutpostsCopyObject.md) and [https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html) in the *Amazon Simple Storage Service API Reference*\.

To enable S3 Replication on Outposts, add a replication rule to your source Outposts bucket\. The replication rule tells S3 on Outposts to replicate objects as specified\. In the replication rule, you must provide the following:
+ **The source Outposts bucket access point** – The access point Amazon Resource Name \(ARN\) or access point alias of the bucket from which you want S3 on Outposts to replicate the objects\. For more information about using access point aliases, see [Using a bucket\-style alias for your S3 on Outposts bucket access point](https://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-outposts-access-points-alias.html)\. 
+ **The objects that you want to replicate** – You can replicate all of the objects in the source Outposts bucket or a subset\. You identify a subset by providing a [key name prefix](https://docs.aws.amazon.com/general/latest/gr/glos-chap.html#keyprefix), one or more object tags, or both in the configuration\.

  For example, if you configure a replication rule to replicate only objects with the key name prefix `Tax/`, S3 on Outposts replicates objects with keys such as `Tax/doc1` or `Tax/doc2`\. But it doesn't replicate objects with the key `Legal/doc3`\. If you specify both a prefix and one or more tags, S3 on Outposts replicates only objects that have the specific key prefix and tags\.
+ **The destination Outposts bucket** – The ARN or access point alias of the bucket to which you want S3 on Outposts to replicate the objects\.

You can configure the replication rule by using the REST API, AWS SDKs, AWS Command Line Interface \(AWS CLI\), or Amazon S3 console\. 

S3 on Outposts also provides API operations to support setting up replication rules\. For more information, see the following topics in the *Amazon Simple Storage Service API Reference*:
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutBucketReplication.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutBucketReplication.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetBucketReplication.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetBucketReplication.html) 
+  [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeleteBucketReplication.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeleteBucketReplication.html) 

**Topics**
+ [Prerequisites for creating replication rules](outposts-replication-prerequisites-config.md)
+ [Creating replication rules on Outposts](replication-between-outposts.md)