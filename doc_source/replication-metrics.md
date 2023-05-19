# Monitoring progress with replication metrics and S3 Event Notifications<a name="replication-metrics"></a>

S3 Replication metrics provide detailed metrics for the replication rules in your replication configuration\. With replication metrics, you can monitor minute\-by\-minute progress by tracking bytes pending, operations pending, operations that failed replication, and replication latency\.

S3 Replication metrics are turned on automatically when you enable S3 Replication Time Control \(S3 RTC\)\. You can also enable S3 Replication metrics independently of S3 RTC while creating or editing a rule\. S3 RTC includes other features, such as a service level agreement \(SLA\) and notifications for missed thresholds\. For more information, see [Meeting compliance requirements using S3 Replication Time Control \(S3 RTC\)](replication-time-control.md)\.

The bytes pending, operations pending, and replication\-latency metrics apply only to new objects that are replicated with S3 Cross\-Region Replication \(S3 CRR\) or S3 Same\-Region Replication \(S3 SRR\)\. The operations failing replication metric tracks both new objects that are replicated with S3 CRR or S3 SRR and existing objects that are replicated with S3 Batch Replication\. To assist in troubleshooting any configuration issues, you can also set up Amazon S3 Event Notifications to receive replication\-failure events\.

When enabled, S3 Replication metrics publish the following metrics to Amazon CloudWatch:
+ **Bytes Pending Replication** – The total number of bytes of objects that are pending replication for a given replication rule\.
+ **Replication Latency** – The maximum number of seconds by which the replication destination buckets are behind the source bucket for a given replication rule\.
+ **Operations Pending Replication** – The number of operations that are pending replication for a given replication rule\. This metric tracks operations related to objects, delete markers, tags, access control lists \(ACLs\), and S3 Object Lock\.
+ **Operations Failed Replication** – The number of operations that failed replication for a given replication rule\. This metric tracks operations related to objects, delete markers, tags, ACLs, and Object Lock\. Unlike the other replication metrics, this metric applies both to new objects that are replicated with S3 CRR or S3 SRR and to existing objects that are replicated with S3 Batch Replication\.

**Note**  
**Operations Failed Replication** tracks S3 Replication failures aggregated at a per\-minute interval\. To identify the specific objects that have failed replication and their failure reasons, subscribe to the `OperationFailedReplication` event in Amazon S3 Event Notifications\. For more information, see [Receiving replication failure events with Amazon S3 Event Notifications](#replication-metrics-events)\.

If a job fails to run at all, metrics are not sent to Amazon CloudWatch\. For example, your job won't run if you don't have the necessary permissions to run an S3 Batch Replication job, or if the tags or prefix in your replication configuration don't match\.

**Topics**
+ [Enabling S3 Replication metrics](#enabling-replication-metrics)
+ [Receiving replication failure events with Amazon S3 Event Notifications](#replication-metrics-events)
+ [Viewing replication metrics by using the Amazon S3 console](viewing-replication-metrics.md)
+ [Amazon S3 replication failure reasons](replication-failure-codes.md)

## Enabling S3 Replication metrics<a name="enabling-replication-metrics"></a>

You can start using S3 Replication metrics with a new or existing replication rule\. You can choose to apply your replication rule to an entire S3 bucket, or to Amazon S3 objects with a specific prefix or tag\.

This topic provides instructions for enabling S3 Replication metrics in your replication configuration when the source and destination buckets are owned by the same or different AWS accounts\.

To enable replication metrics by using the AWS Command Line Interface \(AWS CLI\), you must add a replication configuration to the source bucket with `Metrics` enabled\. In this example configuration, objects under the prefix `Tax` are replicated to the destination bucket `DOC-EXAMPLE-BUCKET`, and metrics are generated for those objects\.

```
{
    "Rules": [
        {
            "Status": "Enabled",
            "Filter": {
                "Prefix": "Tax"
            },
            "Destination": {
                "Bucket": "arn:aws:s3:::DOC-EXAMPLE-BUCKET",
                "Metrics": {
                    "Status": "Enabled"
                }
            },
            "Priority": 1
        }
    ],
    "Role": "IAM-Role-ARN"
}
```

For full instructions on creating replication rules, see [Configuring replication for source and destination buckets owned by the same account](replication-walkthrough1.md)\.

For more information about viewing replication metrics in the S3 console, see [Viewing replication metrics by using the Amazon S3 console](viewing-replication-metrics.md)\.

**Note**  
S3 Replication metrics are billed at the same rate as Amazon CloudWatch custom metrics\. For more information, see [Amazon CloudWatch pricing](https://aws.amazon.com/cloudwatch/pricing/)\.

## Receiving replication failure events with Amazon S3 Event Notifications<a name="replication-metrics-events"></a>

S3 Event Notifications can notify you in instances when objects don't replicate to their destination AWS Region\. Amazon S3 events are available through Amazon Simple Queue Service \(Amazon SQS\), Amazon Simple Notification Service \(Amazon SNS\), or AWS Lambda\. For more information, see [Amazon S3 Event Notifications](EventNotifications.md)\.

For a list of failure codes captured by S3 Event Notifications, see [Amazon S3 replication failure reasons](replication-failure-codes.md)\.