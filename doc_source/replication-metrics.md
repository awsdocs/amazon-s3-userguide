--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Monitoring progress with replication metrics and Amazon S3 event notifications<a name="replication-metrics"></a>

S3 replication metrics provide detailed metrics for the replication rules in your replication configuration\. With replication metrics, you can monitor minute\-by\-minute progress of replication by tracking bytes pending, operations pending, and replication latency\. Additionally, you can set up Amazon S3 Event Notifications to receive replication failure events to assist in troubleshooting any configuration issues\.

When enabled, S3 replication metrics publish the following metrics to Amazon CloudWatch:

**Bytes Pending Replication**—The total number of bytes of objects pending replication for a given replication rule\.

**Replication Latency**—The maximum number of seconds by which the replication destination buckets are behind the source bucket for a given replication rule\.

**Operations Pending Replication**—The number of operations pending replication for a given replication rule\. Operations include objects, delete markers, tags, ACLs, and Object Lock operations\.

**Note**  
S3 replication metrics are billed at the same rate as Amazon CloudWatch custom metrics\. For information, see [Amazon CloudWatch pricing](https://aws.amazon.com/cloudwatch/pricing/)\.

S3 replication metrics are turned on automatically when you enable S3 Replication Time Control \(S3 RTC\)\. S3 RTC includes other features such as a service level agreement \(SLA\) and notifications for missed thresholds\. For more information, see [Meeting compliance requirements using S3 Replication Time Control \(S3 RTC\)](replication-time-control.md)\.

## Receiving replication failure events with Amazon S3 event notifications<a name="replication-metrics-events"></a>

Amazon S3 event notifications can notify you in the rare instance when objects do not replicate to their destination Region\. Amazon S3 events are available through Amazon Simple Queue Service \(Amazon SQS\), Amazon Simple Notification Service \(Amazon SNS\), or AWS Lambda\. For more information, see [Configuring Amazon S3 event notifications](NotificationHowTo.md)\.

**Topics**
+ [Receiving replication failure events with Amazon S3 event notifications](#replication-metrics-events)
+ [Enabling S3 replication metrics](enabling-replication-metrics.md)
+ [Viewing replication metrics using the Amazon S3 console](viewing-replication-metrics.md)