# Receiving S3 on Outposts event notifications using Amazon CloudWatch Events<a name="S3OutpostsNotificationsCWE"></a>

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