# Updating an Amazon S3 Storage Lens dashboard<a name="storage_lens_console_editing"></a>

Use the following steps to update an Amazon S3 Storage Lens dashboard on the Amazon S3 console\.

**To update the dashboard scope and metrics selection**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **S3 Storage Lens**\.

1. Choose the dashboard that you want to edit, and then choose **Edit** at the top of the list\.
**Note**  
You can't change the following:  
The dashboard name 
The home Region
The dashboard scope of the default dashboard, which is scoped to your entire account's storage\.

1. On the dashboard configuration page, in the **General** section, you can update and add tags to your dashboard\.

   You can use tags to manage permissions for your dashboard and to track costs for S3 Storage Lens\. For more information, see [Controlling access using resource tags](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_tags.html) in the *IAM User Guide* and [AWS\-Generated Cost Allocation Tags](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/aws-tags.html) in the *AWS Billing User Guide*\.
**Note**  
You can add up to 50 tags to your dashboard configuration\.

1.  In the **Dashboard scope** section, do the following:

   1.  Update the Regions and buckets that you want S3 Storage Lens to include or exclude in the dashboard\. 
**Note**  
You can either include or exclude Regions and buckets\. This option is limited to Regions only when creating organization\-level dashboards across member accounts in your organization\. 
You can choose up to 50 buckets to include or exclude\.

     Update the buckets in your selected Regions that you want S3 Storage Lens to include or exclude\. You can either include or exclude buckets, but not both\. This option is not present when creating organization\-level dashboards\.

1.  In the **Metrics selection** section, choose the type of metrics that you want to aggregate for this dashboard\.
   + Choose **Free Metrics** to include usage metrics aggregated at the bucket level and 14\-day data retention\. 
   + For an additional charge, choose **Advanced Metrics and Recommendations**\. With Advanced Metrics and Recommendations, you get contextual recommendations that help you optimize storage costs and apply data protection best practices, and data is available for queries for 15 months\. Advanced Metrics and Recommendations also includes usage metrics aggregated at the prefix\-level, activity metrics aggregated by bucket, and Amazon CloudWatch publishing\. For more information, see [Amazon S3 pricing](http://aws.amazon.com/s3/pricing/)\.

1. If you enable Advanced Metrics and Recommendations, you can choose **Advanced metrics and recommendations features**:

   1. Choose **Activity metrics** to track requests and errors for objects in your dashboard scope\. 

   1. Choose **CloudWatch publishing** to publish your S3 Storage Lens metrics to CloudWatch\.

      For more information about the CloudWatch publishing option, see [Monitor S3 Storage Lens metrics in CloudWatch](storage_lens_view_metrics_cloudwatch.md)\.

   1. Choose **Prefix aggregation** to aggregate your usage metrics at the prefix level so that you can receive detailed insights for your top prefixes in each bucket\.
**Note**  
At this time, you can only receive prefix aggregation for usage metrics\. Prefix\-level metrics are not publishing to CloudWatch

1. If you chose to enable prefix aggregation, configure the following:

   1. Choose the minimum prefix threshold size that S3 Storage Lens will collect for this dashboard\. For example, a prefix threshold of 5 percent indicates that prefixes that make up 5 percent or more in size of the storage of the bucket will be aggregated\. 

   1. Choose the prefix depth\. This setting indicates the maximum number of levels up to which the prefixes are evaluated\. The prefix depth must be less than 10\. 

   1. Enter a prefix delimiter character\. This is the value used to identify each prefix level\. The default value in Amazon S3 is the **/** character, but your storage structure might use other delimiter characters\.

**To configure metrics export**

1. Under **Metrics Export**, choose **Enable** if you want to create a metrics export that will be placed daily in a destination bucket of your choice\. The metrics export is in CSV or Apache Parquet format and represents the same scope of data as your S3 Storage Lens dashboard data, without the recommendations\.

1.  If enabled, choose the output format of your daily metrics export\. You can choose between **CSV** or **Apache Parquet**\. Parquet is an open source file format for Hadoop that stores nested data in a flat columnar format\.

1. Update the destination S3 bucket of your metrics export\. You can choose between a bucket in the current account for the S3 Storage Lens dashboard, or choose another AWS account if you have the destination bucket permissions and the destination bucket owner account ID\.

1. Update the destination \(format: `s3://bucket/prefix`\) of the destination S3 bucket\. The bucket address must be in S3 format in the home Region of your S3 Storage Lens dashboard\. 
**Note**  
Amazon S3 will update the permissions policy on the destination bucket to allow S3 to place data in that bucket\. 
 The S3 console will show you the explicit destination bucket permission that will be added by Amazon S3 to the destination bucket policy in the destination bucket permission box\. 
If your metrics export destination S3 bucket has server\-side encryption already enabled, all export files placed there must also have server\-side encryption enabled\. 

1. If you chose to enable server\-side encryption for your dashboard, you must choose an encryption key type\. You can choose between an [Amazon S3 key](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingServerSideEncryption.html) \(SSE\-S3\) and an [AWS Key Management Service \(AWS KMS\)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingKMSEncryption.html) key \(SSE\-KMS\)\.

1.  If you chose an AWS KMS key, you must choose from your KMS keys or enter a key Amazon Resource Name \(ARN\)\.

1. Choose **Save changes**\.

You can then view the metrics included for this dashboard\.