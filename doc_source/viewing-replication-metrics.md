# Viewing replication metrics using the Amazon S3 console<a name="viewing-replication-metrics"></a>

There are three types of Amazon CloudWatch metrics for Amazon S3: storage metrics, request metrics, and replication metrics\. *Replication* metrics are turned on automatically when you enable replication with S3 Replication Time Control \(S3 RTC\) using the AWS Management Console or the Amazon S3 API\. Replication metrics are available 15 minutes after you enable a replication rule with S3 Replication Time Control \(S3 RTC\) \(S3 RTC\)\.

Replication metrics track the rule IDs of the replication configuration\. A replication rule ID can be specific to a prefix, a tag, or a combination of both\. For more information about S3 Replication Time Control \(S3 RTC\), see [Meeting compliance requirements using S3 Replication Time Control \(S3 RTC\)](replication-time-control.md)\.

For more information about CloudWatch metrics for Amazon S3, see [Monitoring metrics with Amazon CloudWatch](cloudwatch-monitoring.md)\.

**Prerequisites**  
Enable a replication rule that has S3 RTC\.

**To view replication metrics**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that contains the objects you want replication metrics for\.

1. Choose the **Metrics** tab\.

1. Under **Replication metrics**, choose **Replication rules**\.

1. Choose **Display charts**\.

   Amazon S3 displays **Replication Latency \(in seconds\)**, **Operations pending replication** in charts\.

1. To view all replication metrics, including **Bytes pending replication**, **Replication Latency \(in seconds\)**, and **Operations pending replication** together on a separate page, choose **View 1 more chart**\.

You can then view the replication metrics **Replication Latency \(in seconds\)**, **Operations pending replication**, and **Bytes pending replication** for the rules that you selected\. Amazon CloudWatch begins reporting replication metrics 15 minutes after you enable S3 RTC on the respective replication rule\. You can view replication metrics on the Amazon S3 or CloudWatch console\. For information, see [Replication metrics with S3 RTC](replication-time-control.md#using-replication-metrics)\.