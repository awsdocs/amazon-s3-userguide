# Creating an Amazon S3 Storage Lens dashboard<a name="storage_lens_console_creating"></a>

**To create an S3 Storage Lens dashboard**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **S3 Storage Lens**\.

1. Choose **Create dashboard**\.

1. On the **Dashboard** page, in the **General** section, do the following:

   1. Enter a dashboard name\. 

      Dashboard names must be fewer than 65 characters and must not contain special characters or spaces\. 
**Note**  
You can't change this dashboard name after the dashboard is created\.

   1. Choose the **Home Region** for your dashboard\. Your dashboard metrics for all included Regions in this dashboard scope are stored centrally in this designated home Region\. 

   1. You can optionally choose to add **Tags** to your dashboard\. You can use tags to manage permissions for your dashboard and track costs for S3 Storage Lens\. 

      For more information, see [Controlling access using resource tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_tags.html) in the *IAM User Guide* and [AWS\-Generated Cost Allocation Tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/aws-tags.html) in the *AWS Billing and Cost Management User Guide*\.
**Note**  
You can add up to 50 tags to your dashboard configuration\.

1.  In the **Dashboard scope** section, do the following:

   1. Choose the Regions and buckets that you want S3 Storage Lens to include or exclude in the dashboard\.

   1. Choose the buckets in your selected Regions that you want S3 Storage Lens to include or exclude\. You can either include or exclude buckets, but not both\. This option is not available when you create organization\-level dashboards\.
**Note**  
You can either include or exclude Regions and buckets\. This option is limited to Regions only when creating organization\-level dashboards across member accounts in your organization\. 
You can choose up to 50 buckets to include or exclude\.

1.  In the **Metrics selection** section, choose the type of metrics that you want to aggregate for this dashboard\.
   + Choose **Free Metrics** to include usage metrics aggregated at the bucket level with 14\-day retention\. 
   + For an additional charge, choose **Advanced Metrics and Recommendations**\. This option includes usage metrics aggregated at the prefix\-level, and activity metrics aggregated by bucket, 15\-month data retention, and contextual recommendations that help you further optimize storage costs and apply data protection best practices\. For more information, see [Amazon S3 pricing](http://aws.amazon.com/s3/pricing/)\.

   If you enable Advanced Metrics and Recommendations, you can choose additional options as follows:

   1. The option to enable **activity** metrics is included with Advanced Metrics and Recommendations\. This option helps you track requests and errors for objects in your dashboard scope\. 

   1. Choose **Enable prefix aggregation** if you want to aggregate your usage metrics at the prefix level so that you can receive detailed insights for your top prefixes in each bucket\.

   1. If you chose to enable prefix aggregation, you must choose the minimum prefix threshold size that S3 Storage Lens will collect for this dashboard\. For example, a prefix threshold of 5 percent indicates that prefixes that make up 5 percent or greater in size of the storage of the bucket will be aggregated\. 

   1. Choose the prefix depth\. This setting indicates the maximum number of levels up to which the prefixes are evaluated\. The prefix depth must be less than 10\. 

   1. Enter a prefix delimiter character\. This is the value used to identify each prefix level\. The default value in Amazon S3 is the **/** character, but your storage structure might use other delimiter characters\.

 You can then view the metrics included for this dashboard\.

**To view metrics for the dashboard**

1.  In the **Metrics Export** section, choose **Enable** to create a metrics export that will be placed daily in a destination bucket of your choice\. 

   The metrics export is in CSV or Apache Parquet format\. It represents the same scope of data as your S3 Storage Lens dashboard data without the recommendations\.

1.  If enabled, choose the output format of your daily metrics export\. You can choose between **CSV** or **Apache Parquet**\. Parquet is an open source file format for Hadoop that stores nested data in a flat columnar format\.

1. Choose the destination S3 bucket for your metrics export\. You can choose a bucket in the current account of the S3 Storage Lens dashboard\. Or you can choose another AWS account if you have the destination bucket permissions and the destination bucket owner account ID\.

1. Choose the destination \(format: `s3://bucket/prefix`\) of the destination S3 bucket\. The bucket address must be in S3 format in the home Region of your S3 Storage Lens dashboard\.
**Note**  
Amazon S3 will update the permissions policy on the destination bucket to allow S3 to place data in that bucket\. 
The S3 console will show you the explicit destination bucket permission that will be added by Amazon S3 to the destination bucket policy in the destination bucket permission box\.
 If your metrics export destination S3 bucket has server\-side encryption already enabled, all export files that are placed there must also have server\-side encryption enabled\. 

1. If you choose to enable server\-side encryption for your dashboard, you must choose an encryption key type\. You can choose between an [Amazon S3 key](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingServerSideEncryption.html) \(SSE\-S3\) and an [AWS Key Management Service \(AWS KMS\)](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingKMSEncryption.html) key \(SSE\-KMS\)\.

1. If you chose an AWS KMS key, you must choose from your KMS master keys or enter a master key Amazon Resource Name \(ARN\)\.