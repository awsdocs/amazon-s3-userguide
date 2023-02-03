# Monitoring metrics with Amazon CloudWatch<a name="cloudwatch-monitoring"></a>

Amazon CloudWatch metrics for Amazon S3 can help you understand and improve the performance of applications that use Amazon S3\. There are several ways that you can use CloudWatch with Amazon S3\.

**Daily storage metrics for buckets**  
Monitor bucket storage using CloudWatch, which collects and processes storage data from Amazon S3 into readable, daily metrics\. These storage metrics for Amazon S3 are reported once per day and are provided to all customers at no additional cost\.

**Request metrics**   
Monitor Amazon S3 requests to quickly identify and act on operational issues\. The metrics are available at 1\-minute intervals after some latency for processing\. These CloudWatch metrics are billed at the same rate as the Amazon CloudWatch custom metrics\. For information about CloudWatch pricing, see [Amazon CloudWatch pricing](https://aws.amazon.com/cloudwatch/pricing/)\. To learn how to opt in to getting these metrics, see [CloudWatch metrics configurations](metrics-configurations.md)\.  
When enabled, request metrics are reported for all object operations\. By default, these 1\-minute metrics are available at the Amazon S3 bucket level\. You can also define a filter for the metrics using a shared prefix, object tag, or access point:  
+ **Access point** – Access points are named network endpoints that are attached to buckets and simplify managing data access at scale for shared datasets in S3\. With the access point filter, you can gain insights into your access point usage\. For more information about access points, see [Monitoring and logging access points](access-points-monitoring-logging.md)\.
+ **Prefix** – Although the Amazon S3 data model is a flat structure, you can use prefixes to infer a hierarchy\. A prefix is similar to a directory name that enables you to group similar objects together in a bucket\. The S3 console supports prefixes with the concept of folders\. If you filter by prefix, objects that have the same prefix are included in the metrics configuration\. For more information about prefixes, see [Organizing objects using prefixes](using-prefixes.md)\. 
+ **Tags** – Tags are key\-value name pairs that you can add to objects\. Tags help you find and organize objects easily\. You can also use tags as a filter for metrics configurations so that only objects with those tags are included in the metrics configuration\. For more information about object tags, see [Categorizing your storage using tags](object-tagging.md)\. 
To align these metrics to specific business applications, workflows, or internal organizations, you can filter on a shared prefix, object tag, or access point\. 

**Replication metrics**  
**Replication metrics** – Monitor the total number of S3 API operations that are pending replication, the total size of objects pending replication, and the maximum replication time to the destination Region\. Replication rules that have S3 Replication Time Control \(S3 RTC\) or S3 Replication metrics enabled will publish replication metrics\.   
For more information, see [Monitoring progress with replication metrics and Amazon S3 event notifications](replication-metrics.md) or [Meeting compliance requirements using S3 Replication Time Control \(S3 RTC\)](replication-time-control.md)\.

**Amazon S3 Storage Lens metrics**  
You can publish S3 Storage Lens usage and activity metrics to Amazon CloudWatch to create a unified view of your operational health in CloudWatch [dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html)\. S3 Storage Lens metrics are available in the `AWS/S3/Storage-Lens` namespace\. The CloudWatch publishing option is available for S3 Storage Lens dashboards upgraded to *advanced metrics and recommendations*\. You can enable the CloudWatch publishing option for a new or existing dashboard configuration in S3 Storage Lens\.  
For more information, see [Monitor S3 Storage Lens metrics in CloudWatch](storage_lens_view_metrics_cloudwatch.md)\.

All CloudWatch statistics are retained for a period of 15 months so that you can access historical information and gain a better perspective on how your web application or service is performing\. For more information, see [What is Amazon CloudWatch?](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html) in the *Amazon CloudWatch User Guide*\.

**Best\-effort CloudWatch metrics delivery**  
 CloudWatch metrics are delivered on a best\-effort basis\. Most requests for an Amazon S3 object that have request metrics result in a data point being sent to CloudWatch\.

The completeness and timeliness of metrics are not guaranteed\. The data point for a particular request might be returned with a timestamp that is later than when the request was actually processed\. The data point for a minute might be delayed before being available through CloudWatch, or it might not be delivered at all\. CloudWatch request metrics give you an idea of the nature of traffic against your bucket in near\-real time\. It is not meant to be a complete accounting of all requests\.

It follows from the best\-effort nature of this feature that the reports available at the [Billing & Cost Management Dashboard](https://console.aws.amazon.com/billing/home?#/) might include one or more access requests that do not appear in the bucket metrics\.

For more information, see the following topics\.

**Topics**
+ [Metrics and dimensions](metrics-dimensions.md)
+ [Accessing CloudWatch metrics](cloudwatch-monitoring-accessing.md)
+ [CloudWatch metrics configurations](metrics-configurations.md)