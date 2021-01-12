--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Meeting compliance requirements using S3 Replication Time Control \(S3 RTC\)<a name="replication-time-control"></a>

S3 Replication Time Control \(S3 RTC\) helps you meet compliance or business requirements for data replication and provides visibility into Amazon S3 replication times\. S3 RTC replicates most objects that you upload to Amazon S3 in seconds, and 99\.99 percent of those objects within 15 minutes\. 

S3 RTC includes replication metrics, with which you can monitor the total number of S3 API operations that are pending replication, the total size of objects pending replication, and the maximum replication time to the destination Region\.

With S3 RTC, Amazon S3 events can notify you in the rare instance when objects do not replicate within 15 minutes and when those objects replicate successfully to their destination Region\. Amazon S3 events are available through Amazon SQS, Amazon SNS, or AWS Lambda\. For more information, see [Configuring Amazon S3 event notifications](NotificationHowTo.md)\.

**Topics**
+ [Enabling S3 Replication Time Control](#enabling-replication-time-control)
+ [Replication metrics overview](#using-replication-metrics)
+ [Using Amazon S3 event notifications to track replication objects](#using-s3-events-to-track-rtc)
+ [Best practices and guidelines for S3 RTC](#rtc-best-practices)

## Enabling S3 Replication Time Control<a name="enabling-replication-time-control"></a>

You can start using S3 Replication Time Control \(S3 RTC\) with a new or existing replication rule\. You can choose to apply your replication rule to an entire S3 bucket, or to Amazon S3 objects with a specific prefix or tag\. When you enable S3 RTC, replication metrics are also enabled on your replication rule\. 

 Replication configurations with S3 Replication Time Control \(S3 RTC\) specified are written in the latest XML V2\. XML V2 replication configurations are those that contain the `Filter` element for rules\. In V2 replication configurations, Amazon S3 doesn't replicate delete markers\. Therefore, you must set the `DeleteMarkerReplication` element to `Disabled`\.

**Note**  
 Replication metrics are billed at the same rate as Amazon CloudWatch custom metrics\. For information, see [Amazon CloudWatch pricing](https://aws.amazon.com/cloudwatch/pricing/)\. 

You can configure Replication Time Control using the [Amazon S3 console](https://console.aws.amazon.com/s3/), the [Amazon S3 API](https://docs.aws.amazon.com/AmazonS3/latest/API/), the [AWS SDKs](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingAWSSDK.html), and the [AWS Command Line Interface \(AWS CLI\)](https://docs.aws.amazon.com/cli/latest/reference/)\.

For more information about creating a rule with S3 RTC, see [Replicating objects with S3 Replication Time Control \(S3 RTC\)](replication-walkthrough-5.md)\.

## Replication metrics overview<a name="using-replication-metrics"></a>

Replication rules with S3 Replication Time Control \(S3 RTC\) enabled publishes replication metrics\. With replication metrics, you can monitor the total number of S3 API operations that are pending replication, the total size of objects pending replication, and the maximum replication time to the destination Region\. You can then monitor each dataset that you replicate separately\.

Replication metrics are available within 15 minutes of enabling S3 RTC\. Replication metrics are available through the [Amazon S3 console](https://console.aws.amazon.com/s3/), the [Amazon S3 API](https://docs.aws.amazon.com/AmazonS3/latest/API/), the [AWS SDKs](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingAWSSDK.html), the [AWS Command Line Interface \(AWS CLI\)](https://docs.aws.amazon.com/cli/latest/reference/), and [Amazon CloudWatch](https://docs.aws.amazon.com/AmazonCloudWatch/latest/DeveloperGuide/)\. For more information, see [Monitoring metrics with Amazon CloudWatch](cloudwatch-monitoring.md)\.

For more information about finding replication metrics via the Amazon S3 console, see [Viewing replication metrics using the Amazon S3 console](viewing-replication-metrics.md)\.

## Using Amazon S3 event notifications to track replication objects<a name="using-s3-events-to-track-rtc"></a>

You can track replication time for objects that did not replicate within 15 minutes by monitoring specific event notifications that S3 Replication Time Control \(S3 RTC\) publishes\. These events are published when an object that was eligible for replication using S3 RTC didn't replicate within 15 minutes, and when that object replicates to the destination Region\. 

Replication events are available within 15 minutes of enabling S3 RTC\. Amazon S3 events are available through Amazon SQS, Amazon SNS, or AWS Lambda\. For more information, see [Configuring Amazon S3 event notifications](NotificationHowTo.md)\.

## Best practices and guidelines for S3 RTC<a name="rtc-best-practices"></a>

When replicating data in Amazon S3 using S3 Replication Time Control \(S3 RTC\), follow these best practice guidelines to optimize replication performance for your workloads\. 

**Topics**
+ [Amazon S3 Replication and request rate performance guidelines](#rtc-request-rate-performance)
+ [Estimating your replication request rates](#estimating-replication-request-rates)
+ [Exceeding S3 RTC data transfer rate limits](#exceed-rtc-data-transfer-limits)
+ [AWS KMS encrypted object replication request rates](#kms-object-replication-request-rates)

### Amazon S3 Replication and request rate performance guidelines<a name="rtc-request-rate-performance"></a>

When uploading and retrieving storage from Amazon S3, your applications can achieve thousands of transactions per second in request performance\. For example, an application can achieve at least 3,500 PUT/COPY/POST/DELETE or 5,500 GET/HEAD requests per second per prefix in an S3 bucket, including the requests that S3 replication makes on your behalf\. There are no limits to the number of prefixes in a bucket\. You can increase your read or write performance by parallelizing reads\. For example, if you create 10 prefixes in an S3 bucket to parallelize reads, you could scale your read performance to 55,000 read requests per second\. 

Amazon S3 automatically scales in response to sustained request rates above these guidelines, or sustained request rates concurrent with LIST requests\. While Amazon S3 is internally optimizing for the new request rate, you might receive HTTP 503 request responses temporarily until the optimization is complete\. This might occur with increases in request per second rates, or when you first enable S3 RTC\. During these periods, your replication latency might increase\. The S3 RTC service level agreement \(SLA\) doesn’t apply to time periods when Amazon S3 performance guidelines on requests per second are exceeded\. 

The S3 RTC SLA also doesn’t apply during time periods where your replication data transfer rate exceeds the default 1 Gbps limit\. If you expect your replication transfer rate to exceed 1 Gbps, you can contact [AWS Support Center](https://console.aws.amazon.com/support/home#/) or use [Service Quotas](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html) to request an increase in your limit\. 

### Estimating your replication request rates<a name="estimating-replication-request-rates"></a>

Your total request rate including the requests that Amazon S3 replication makes on your behalf should be within the Amazon S3 request rate guidelines for both the replication source and destination buckets\. For each object replicated, Amazon S3 replication makes up to five GET/HEAD requests and one PUT request to the source bucket, and one PUT request to each destination bucket\.

For example, if you expect to replicate 100 objects per second, Amazon S3 replication might perform an additional 100 PUT requests on your behalf for a total of 200 PUTs per second to the source S3 bucket\. Amazon S3 replication also might perform up to 500 GET/HEAD \(5 GET/HEAD requests for each object replicated\.\) 

**Note**  
You incur costs for only one PUT request per object replicated\. For more information, see the pricing information in the [Amazon S3 FAQ on replication](https://aws.amazon.com/s3/faqs/#Replication)\. 

### Exceeding S3 RTC data transfer rate limits<a name="exceed-rtc-data-transfer-limits"></a>

If you expect your S3 Replication Time Control data transfer rate to exceed the default 1 Gbps limit, contact [AWS Support Center](https://console.aws.amazon.com/support/home#/) or use [Service Quotas](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html) to request an increase in your limit\. 

### AWS KMS encrypted object replication request rates<a name="kms-object-replication-request-rates"></a>

When you replicate objects encrypted with server\-side encryption \(SSE\-KMS\) using Amazon S3 replication, AWS Key Management Service \(AWS KMS\) requests per second limits apply\. AWS KMS might reject an otherwise valid request because your request rate exceeds the limit for the number of requests per second\. When a request is throttled, AWS KMS returns a `ThrottlingException` error\. The AWS KMS request rate limit applies to requests you make directly and to requests made by Amazon S3 replication on your behalf\. 

For example, if you expect to replicate 1,000 objects per second, you can subtract 2,000 requests from your AWS KMS request rate limit\. The resulting request rate per second is available for your AWS KMS workloads excluding replication\. You can use [AWS KMS request metrics in Amazon CloudWatch](https://docs.aws.amazon.com/kms/latest/developerguide/monitoring-cloudwatch.html) to monitor the total AWS KMS request rate on your AWS account\.