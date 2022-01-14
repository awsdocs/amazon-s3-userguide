# Amazon S3 Event Notifications<a name="NotificationHowTo"></a>

You can use the Amazon S3 Event Notifications feature to receive notifications when certain events happen in your S3 bucket\. To enable notifications, add a notification configuration that identifies the events that you want Amazon S3 to publish\. Make sure that it also identifies the destinations where you want Amazon S3 to send the notifications\. You store this configuration in the *notification* subresource that's associated with a bucket\. For more information, see [Bucket configuration options](UsingBucket.md#bucket-config-options-intro)\. Amazon S3 provides an API for you to manage this subresource\. 

**Important**  
Amazon S3 event notifications are designed to be delivered at least once\. Typically, event notifications are delivered in seconds but can sometimes take a minute or longer\. 

## Overview of Amazon S3 Event Notifications<a name="notification-how-to-overview"></a>

Currently, Amazon S3 can publish notifications for the following events:
+ New object created events
+ Object removal events
+ Restore object events
+ Reduced Redundancy Storage \(RRS\) object lost events
+ Replication events
+ S3 Lifecycle expiration events
+ S3 Lifecycle transition events
+ S3 Intelligent\-Tiering automatic archival events
+ Object tagging events
+ Object ACL PUT events

For full descriptions of all the supported event types, see [Supported event types for SQS, SNS, and Lambda](notification-how-to-event-types-and-destinations.md#supported-notification-event-types)\.

Amazon S3 can send event notification messages to the following destinations\. You specify the Amazon Resource Name \(ARN\) value of these destinations in the notification configuration\.
+ Amazon Simple Notification Service \(Amazon SNS\) topics
+ Amazon Simple Queue Service \(Amazon SQS\) queues
+ AWS Lambda function

For more information, see [Supported event destinations](notification-how-to-event-types-and-destinations.md#supported-notification-destinations)\.

**Warning**  
If your notification writes to the same bucket that triggers the notification, it could cause an execution loop\. For example, if the bucket triggers a Lambda function each time an object is uploaded, and the function uploads an object to the bucket, then the function indirectly triggers itself\. To avoid this, use two buckets, or configure the trigger to only apply to a prefix used for incoming objects\.  
For more information and an example of using Amazon S3 notifications with AWS Lambda, see [Using AWS Lambda with Amazon S3](https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html) in the *AWS Lambda Developer Guide*\. 

For more information about the number of event notification configurations that you can create per bucket, see [Amazon S3 service quotas](https://docs.aws.amazon.com/general/latest/gr/s3.html#limits_s3) in *AWS General Reference*\.

For more information about event notifications, see the following sections\.

**Topics**
+ [Overview of Amazon S3 Event Notifications](#notification-how-to-overview)
+ [Event notification types and destinations](notification-how-to-event-types-and-destinations.md)
+ [Using Amazon SQS, Amazon SNS, and Lambda](how-to-enable-disable-notification-intro.md)
+ [Using EventBridge](EventBridge.md)