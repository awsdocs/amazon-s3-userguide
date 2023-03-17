# View the replication rules for your Multi\-Region Access Point<a name="mrap-view-replication-rules"></a>

With Multi\-Region Access Points, you can either set up one\-way replication rules or two\-way \(bidirectional\) replication rules\. For information about how to manage your replication rules, see [ Managing replication rules by using the Amazon S3 console](https://docs.aws.amazon.com/AmazonS3/latest/userguide/disable-replication.html)\.

**To view the replication rules for your Multi\-Region Access Point**



1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Multi\-Region Access Points**\.

1. Choose the name of your Multi\-Region Access Point\.

1. Choose the **Replication and failover** tab\.

1. Scroll down to the **Replication rules** section\. This section lists all of the replication rules that have been created for your Multi\-Region Access Point\.
**Note**  
If you’ve added a bucket from another account to this Multi\-Region Access Point, you must have the `s3:GetBucketReplication` permission from the bucket owner to view the replication rules for that bucket\.