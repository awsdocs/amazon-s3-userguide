# Receiving S3 on Outposts event notifications using Amazon CloudWatch Events<a name="S3OutpostsNotificationsCWE"></a>

You can use CloudWatch Events to create a rule for any Amazon S3 on Outposts API event to get notified through all supported CloudWatch targets, including Amazon Simple Queue Service \(Amazon SQS\), Amazon Simple Notification Service \(Amazon SNS\), and AWS Lambda\. For more information, see the list of [AWS services that can be targets for CloudWatch Events](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/WhatIsCloudWatchEvents.html) in the *Amazon CloudWatch Events User Guide*\. To choose a target service to work with your S3 on Outposts, see [Creating a CloudWatch Events rule that triggers on an AWS API call using AWS CloudTrail](https://docs.aws.amazon.com/AmazonCloudWatch/latest/events/Create-CloudWatch-Events-CloudTrail-Rule.html) in the *Amazon CloudWatch Events User Guide*\.

**Note**  
For S3 on Outposts object operations, AWS API call events sent by CloudTrail will only match your rules if you have trails \(optionally with event selectors\) configured to receive those events\. For more information, see [Working with CloudTrail log files](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/create-event-selectors-for-a-trail.html) in the *AWS CloudTrail User Guide*\.

**Example**  
The following is a sample rule for the `DeleteObject` operation\. To use this sample rule, replace *DOC\-EXAMPLE\-BUCKET1* with the name of your S3 on Outposts bucket\.   

```
{
  "source": [
    "aws.s3-outposts"
  ],
  "detail-type": [
    "AWS API call through CloudTrail"
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