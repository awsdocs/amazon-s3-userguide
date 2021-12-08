# Monitoring Amazon S3 on Outposts<a name="MonitoringS3Outposts"></a>

You can monitor and manage Amazon S3 on Outposts storage capacity using Amazon CloudWatch Events and AWS CloudTrail logs\.

**Topics**
+ [Managing S3 on Outposts capacity with Amazon CloudWatch metrics](#S3OutpostsCapacity)
+ [Managing S3 on Outposts capacity with AWS CloudTrail logs](#S3OutpostsCloudtrail)
+ [Receiving S3 on Outposts event notifications using Amazon CloudWatch Events](#S3OutpostsNotificationsCWE)

## Managing S3 on Outposts capacity with Amazon CloudWatch metrics<a name="S3OutpostsCapacity"></a>

If there is not enough space to store an object on your Outpost, the API returns an insufficient capacity exemption \(ICE\)\. To avoid this, you can create CloudWatch alerts that tell you when storage utilization exceeds a threshold\. For more information, see [Amazon S3 on Outposts metrics in CloudWatch](metrics-dimensions.md#s3-outposts-cloudwatch-metrics)\. 

You can use this method to free up space by explicitly deleting data, using a lifecycle expiration policy, or copying data from your S3 on Outposts bucket to an S3 bucket in an AWS Region using AWS DataSync\. For more information about using DataSync, see [Getting Started with AWS DataSync](https://docs.aws.amazon.com/datasync/latest/userguide/getting-started.html) in the *AWS DataSync User Guide*\.

## Managing S3 on Outposts capacity with AWS CloudTrail logs<a name="S3OutpostsCloudtrail"></a>

Your Amazon S3 on Outposts management events are available via AWS CloudTrail logs\. For more information, see [Logging Amazon S3 API calls using CloudTrail](https://docs.aws.amazon.com/AmazonS3/latest/dev/cloudtrail-logging.html)\. 

In addition, you can optionally enable logging for data events in CloudTrail\. For more information, see [Enable logging for objects in a bucket using the console](enable-cloudtrail-logging-for-s3.md#enable-cloudtrail-events)\. 

## Receiving S3 on Outposts event notifications using Amazon CloudWatch Events<a name="S3OutpostsNotificationsCWE"></a>

You can use CloudWatch Events to create a rule for any S3 on Outposts API event to get notified via all supported CloudWatch targets, including Amazon SQS, Amazon SNS, and AWS Lambda\. For more information, see the list of [AWS services that can be targets for CloudWatch Events](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html)\. To choose a target service to work with your S3 on Outposts, see [Creating a CloudWatch Events rule that triggers on an AWS API call Using AWS CloudTrail](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/Create-CloudWatch-Events-CloudTrail-Rule.html)\.

**Note**  
For S3 on Outposts object operations, AWS API call events sent by CloudTrail will only match your rules if you have trails \(optionally with event selectors\) configured to receive those events\. For more information, see [Working with CloudTrail log files](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/create-event-selectors-for-a-trail.html)\.

**Example**  
The following is a sample rule for the `DeleteObject` operation\.  

```
{
  "source": [
    "aws.s3-outposts"
  ],
  "detail-type": [
    "AWS API call via CloudTrail"
  ],
  "detail": {
    "eventSource": [
      "s3-outposts.amazonaws.com"
    ],
    "eventName": [
      "DeleteObject"
    ],
    "requestParameters": {
      "bucketName": [
        "DOC-EXAMPLE-BUCKET1"
      ]
    }
  }
}
```