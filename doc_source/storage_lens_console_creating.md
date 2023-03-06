# Creating an Amazon S3 Storage Lens dashboard<a name="storage_lens_console_creating"></a>

Use the following steps to create an Amazon S3 Storage Lens dashboard on the Amazon S3 console\.

**Step 1: Define the dashboard scope**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, under **S3 Storage Lens**, choose **Dashboards**\.

1. Choose **Create dashboard**\.

1. On the **Dashboard** page, in the **General** section, do the following:

   1. Enter a dashboard name\. 

      Dashboard names must be fewer than 65 characters and must not contain special characters or spaces\. 
**Note**  
You can't change this dashboard name after the dashboard is created\.

   1. Choose the **Home Region** for your dashboard\. Your dashboard metrics for all included Regions in this dashboard scope are stored centrally in this designated home Region\. 

   1. You can optionally choose to add **Tags** to your dashboard\. You can use tags to manage permissions for your dashboard and track costs for S3 Storage Lens\. 

      For more information, see [Controlling access using resource tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_tags.html) in the *IAM User Guide* and [AWS\-Generated Cost Allocation Tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/aws-tags.html) in the *AWS Billing User Guide*\.
**Note**  
You can add up to 50 tags to your dashboard configuration\.

1.  In the **Dashboard scope** section, do the following:

   1. Choose the Regions and buckets that you want S3 Storage Lens to include or exclude in the dashboard\.

   1. Choose the buckets in your selected Regions that you want S3 Storage Lens to include or exclude\. You can either include or exclude buckets, but not both\. This option is not available when you create organization\-level dashboards\.
**Note**  
You can either include or exclude Regions and buckets\. This option is limited to Regions only when creating organization\-level dashboards across member accounts in your organization\. 
You can choose up to 50 buckets to include or exclude\.

**Step 2: Configure the metrics selection**

1. In the **Metrics selection** section, choose the type of metrics that you want to aggregate for this dashboard\.
   + To include free metrics aggregated at the bucket level and available for queries for 14 days, choose **Free metrics**\.
   + To enable advanced metrics and other advanced options, choose **Advanced metrics and recommendations**\. These options include advanced prefix aggregation, Amazon CloudWatch publishing, and contextual recommendations\. Data is available for queries for 15 months\. Advanced metrics and recommendations have an additional cost\. For more information, see [ Amazon S3 pricing](http://aws.amazon.com/s3/pricing/)\. 

     For more information about advanced metrics and free metrics, see [Metrics selection](storage_lens_basics_metrics_recommendations.md#storage_lens_basics_metrics_selection)\.

1. Under **Advanced metrics and recommendations features**, select the options that you want to enable:
   + **Advanced metrics** 
   + **CloudWatch publishing**
   + **Prefix aggregation**
**Important**  
If you enable prefix aggregation for your S3 Storage Lens configuration, prefix\-level metrics will not be published to CloudWatch\. Only bucket, account, and organization\-level S3 Storage Lens metrics are published to CloudWatch\.

1. If you enabled **Advanced metrics**, select the **Advanced metrics categories** that you want to display in your S3 Storage Lens dashboard:
   + **Activity metrics**
   + **Detailed status code metrics**
   + **Advanced cost optimization metrics**
   + **Advanced data protection metrics**

   For more information about metrics categories, see [Metrics categories](storage_lens_basics_metrics_recommendations.md#storage_lens_basics_metrics_types)\. For a complete list of metrics, see [Amazon S3 Storage Lens metrics glossary](storage_lens_metrics_glossary.md)\.

1. If you chose to enable prefix aggregation, configure the following:

   1. Choose the minimum prefix threshold size for this dashboard\. 

      For example, a prefix threshold of 5 percent indicates that prefixes that make up 5 percent or more of the bucket's total storage size will be aggregated\. 

   1. Choose the prefix depth\. 

      This setting indicates the maximum number of levels up to which the prefixes are evaluated\. The prefix depth must be less than 10\. 

   1. Enter a prefix delimiter character\. 

      This value is used to identify each prefix level\. The default value in Amazon S3 is the `/` character, but your storage structure might use other delimiter characters\.

**\(Optional\) Step 3: Export metrics for the dashboard**

1. In the **Metrics export** section, to create a metrics export that will be placed daily in a destination bucket of your choice, choose **Enable**\. 

   The metrics export is in CSV or Apache Parquet format\. It represents the same scope of data as your S3 Storage Lens dashboard data without the recommendations\.

1. If you enabled the metrics export, choose the output format of your daily metrics export: **CSV** or **Apache Parquet**\. 

   Parquet is an open source file format for Hadoop that stores nested data in a flat columnar format\.

1. Choose the destination S3 bucket for your metrics export\. 

   You can choose a bucket in the current account of the S3 Storage Lens dashboard\. Or you can choose another AWS account if you have the destination bucket permissions and the destination bucket owner's account ID\.

1. Choose the destination S3 bucket \(format: `s3://bucket-name/prefix`\)\. 

   The bucket must be in the home Region of your S3 Storage Lens dashboard\. The S3 console shows you the **Destination bucket permission** that will be added by Amazon S3 to the destination bucket policy\. Amazon S3 updates the bucket policy on the destination bucket to allow S3 to place data in that bucket\. 

1. \(Optional\) To enable server\-side encryption for your metrics export, choose **Specify an encryption key**\. Then, choose the encryption key type: **Amazon S3 managed keys \(SSE\-S3\)** or **AWS Key Management Service key \(SSE\-KMS\)**\. 

   You can choose between an [Amazon S3 managed key](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingServerSideEncryption.html) \(SSE\-S3\) and an [AWS Key Management Service \(AWS KMS\)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingKMSEncryption.html) key \(SSE\-KMS\)\.

1. \(Optional\) To specify an AWS KMS key, you must choose a KMS key or enter a key Amazon Resource Name \(ARN\)\.

   If you choose a customer managed key, you must grant S3 Storage Lens permission to encrypt in the AWS KMS key policy\. For more information, see [Using an AWS KMS key to encrypt your metrics exports](storage_lens_encrypt_permissions.md)

1. Choose **Create dashboard**\.