# Monitoring metrics with Amazon CloudWatch<a name="cloudwatch-monitoring"></a>

Amazon CloudWatch metrics for Amazon S3 can help you understand and improve the performance of applications that use Amazon S3\. There are several ways that you can use CloudWatch with Amazon S3\.
+ **Daily storage metrics for buckets** ‐ Monitor bucket storage using CloudWatch, which collects and processes storage data from Amazon S3 into readable, daily metrics\. These storage metrics for Amazon S3 are reported once per day and are provided to all customers at no additional cost\.
+ **Request metrics** ‐ Monitor Amazon S3 requests to quickly identify and act on operational issues\. These metrics are available at 1\-minute intervals after some latency to process them\. These CloudWatch metrics are billed at the same rate as Amazon CloudWatch custom metrics\. For information about CloudWatch pricing, see [Amazon CloudWatch pricing](https://aws.amazon.com/cloudwatch/pricing/)\. To learn how to opt in to getting these metrics, see [CloudWatch metrics configurations](metrics-configurations.md)\.

  When enabled, request metrics are reported for all object operations\. By default, these 1\-minute metrics are available at the Amazon S3 bucket level\. You can also define a filter for the metrics collected using a shared prefix or object tag\. Using a shared prefix or object tag allows you to align metrics filters to specific business applications, workflows, or internal organizations\.
+ **Replication metrics** ‐ Monitor the total number of S3 API operations that are pending replication, the total size of objects pending replication, and the maximum replication time to the destination Region\. Replication rules that have S3 Replication Time Control \(S3 RTC\) or S3 replication metrics enabled will publish replication metrics\. 

  For more information, see [Monitoring progress with replication metrics and Amazon S3 event notifications](replication-metrics.md) or [Meeting compliance requirements using S3 Replication Time Control \(S3 RTC\)](replication-time-control.md)\.

All CloudWatch statistics are retained for a period of 15 months so that you can access historical information and gain a better perspective on how your web application or service is performing\. For more information, see [What is Amazon CloudWatch?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/WhatIsCloudWatch.html) in the *Amazon CloudWatch User Guide*\.

For more information, see the topics below\.

**Topics**
+ [Metrics and dimensions](metrics-dimensions.md)
+ [Accessing CloudWatch metrics](cloudwatch-monitoring-accessing.md)
+ [CloudWatch metrics configurations](metrics-configurations.md)