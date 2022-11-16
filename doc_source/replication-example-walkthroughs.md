# Walkthroughs: Examples for configuring replication<a name="replication-example-walkthroughs"></a>

The following examples show how to configure live replication for common use cases\. The examples demonstrate replication configuration using the Amazon S3 console, AWS Command Line Interface \(AWS CLI\), and AWS SDKs \(Java and \.NET SDK examples are shown\)\. For information about installing and configuring the AWS CLI, see the following topics in the *AWS Command Line Interface User Guide*\.

**Note**  
Live replication refers to Same\-Region Replication \(SRR\) and Cross\-Region Replication \(CRR\)\. For an on\-demand replication action to sync buckets and replicate existing objects, see [Replicate existing objects](s3-batch-replication-batch.md)\.
+  [Installing the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/installing.html) 
+  [Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-started.html) – You must set up at least one profile\. If you are exploring cross\-account scenarios, set up two profiles\.

For information about AWS SDKs, see [AWS SDK for Java](https://aws.amazon.com/sdk-for-java/) and [AWS SDK for \.NET](https://aws.amazon.com/sdk-for-net/)\.

To learn more about how to use S3 Replication to replicate data, see [Tutorial: Replicating data within and between AWS Regions using S3 Replication](http://aws.amazon.com/getting-started/hands-on/replicate-data-using-amazon-s3-replication/?ref=docs_gateway/amazons3/replication-example-walkthroughs.html)\.

**Topics**
+ [Configuring for buckets in the same account](replication-walkthrough1.md)
+ [Configuring for buckets in different accounts](replication-walkthrough-2.md)
+ [Changing replica owner](replication-walkthrough-3.md)
+ [Replicating encrypted objects](replication-walkthrough-4.md)
+ [Using S3 Replication Time Control](replication-walkthrough-5.md)
+ [Managing replication rules](disable-replication.md)