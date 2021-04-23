# Configuring storage class analysis<a name="configure-analytics-storage-class"></a>

By using the Amazon S3 analytics storage class analysis tool, you can analyze storage access patterns to help you decide when to transition the right data to the right storage class\. Storage class analysis observes data access patterns to help you determine when to transition less frequently accessed STANDARD storage to the STANDARD\_IA \(IA, for infrequent access\) storage class\. For more information about STANDARD\_IA, see the [Amazon S3 FAQ](https://aws.amazon.com/s3/faqs/#sia) and [Using Amazon S3 storage classes](storage-class-intro.md)\.

You set up storage class analysis by configuring what object data you want to analyze\. You can configure storage class analysis to do the following:
+ **Analyze the entire contents of a bucket\.**

  You'll receive an analysis for all the objects in the bucket\.
+ **Analyze objects grouped together by prefix and tags\.**

  You can configure filters that group objects together for analysis by prefix, or by object tags, or by a combination of prefix and tags\. You receive a separate analysis for each filter you configure\. You can have multiple filter configurations per bucket, up to 1,000\. 
+ **Export analysis data\.** 

  When you configure storage class analysis for a bucket or filter, you can choose to have the analysis data exported to a file each day\. The analysis for the day is added to the file to form a historic analysis log for the configured filter\. The file is updated daily at the destination of your choice\. When selecting data to export, you specify a destination bucket and optional destination prefix where the file is written\.

You can use the Amazon S3 console, the REST API, or the AWS CLI or AWS SDKs to configure storage class analysis\.

**Important**  
Storage class analysis does not give recommendations for transitions to the ONEZONE\_IA or S3 Glacier storage classes\.  
If you want to configure storage class analysis to export your findings as a \.csv file and the destination bucket uses default bucket encryption with a AWS KMS customer master key \(CMK\), you must update the AWS KMS key policy to grant Amazon S3 permission to encrypt the \.csv file\. For instructions, see [Granting Amazon S3 permission to use your AWS KMS CMK for encryption](configure-inventory.md#configure-inventory-kms-key-policy)\.

For more information about analytics, see [Amazon S3 analytics – Storage Class Analysis](analytics-storage-class.md)\.

## Using the S3 console<a name="storage-class-analysis-console"></a>

**To configure storage class analysis**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket for which you want to configure storage class analysis\.

1. Choose the **Metrics** tab\.

1. Under **Storage Class Analysis**, choose **Create analytics configuration**\.

1. Type a name for the filter\. If you want to analyze the whole bucket, leave the **Prefix** field empty\.

1. In the **Prefix** field, type text for the prefix for the objects that you want to analyze\.

1. To add a tag, choose **Add tag**\. Enter a key and value for the tag\. You can enter one prefix and multiple tags\.

1. Optionally, you can choose **Enable** under **Export CSV** to export analysis reports to a comma\-separated values \(\.csv\) flat file\. Choose a destination bucket where the file can be stored\. You can type a prefix for the destination bucket\. The destination bucket must be in the same AWS Region as the bucket for which you are setting up the analysis\. The destination bucket can be in a different AWS account\. 

   If the destination bucket for the \.csv file uses default bucket encryption with a AWS KMS CMK, you must update the AWS KMS key policy to grant Amazon S3 permission to encrypt the \.csv file\. For instructions, see [Granting Amazon S3 permission to use your AWS KMS CMK for encryption](configure-inventory.md#configure-inventory-kms-key-policy)\.

1. Choose **Create Configuration**\.

 Amazon S3 creates a bucket policy on the destination bucket that grants Amazon S3 write permission\. This allow it to write the export data to the bucket\. 

 If an error occurs when you try to create the bucket policy, you'll be given instructions on how to fix it\. For example, if you chose a destination bucket in another AWS account and do not have permissions to read and write to the bucket policy, you'll see the following message\. You must have the destination bucket owner add the displayed bucket policy to the destination bucket\. If the policy is not added to the destination bucket you won’t get the export data because Amazon S3 doesn’t have permission to write to the destination bucket\. If the source bucket is owned by a different account than that of the current user, then the correct account ID of the source bucket must be substituted in the policy\.

For information about the exported data and how the filter works, see [Amazon S3 analytics – Storage Class Analysis](analytics-storage-class.md)\.

## Using the REST API<a name="storage-class-apis"></a>

To configure Storage Class Analysis using the REST API, use the [PutBucketAnalyticsConfiguration](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTAnalyticsConfig.html)\. You can also use the equivalent operation with the AWS CLI or AWS SDKs\. 

You can use the following REST APIs to work with Storage Class Analysis:
+  [ DELETE Bucket Analytics configuration](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketDELETEAnalyticsConfiguration.html) 
+  [ GET Bucket Analytics configuration](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETAnalyticsConfig.html) 
+  [ List Bucket Analytics Configuration](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketListAnalyticsConfigs.html) 