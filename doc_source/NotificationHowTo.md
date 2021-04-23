# Amazon S3 Event Notifications<a name="NotificationHowTo"></a>

You can use the Amazon S3 Event Notifications feature to receive notifications when certain events happen in your S3 bucket\. To enable notifications, you must first add a notification configuration that identifies the events you want Amazon S3 to publish and the destinations where you want Amazon S3 to send the notifications\. You store this configuration in the *notification* subresource that is associated with a bucket\. For more information, see [Bucket configuration options](UsingBucket.md#bucket-config-options-intro)\. Amazon S3 provides an API for you to manage this subresource\. 

**Important**  
Amazon S3 event notifications are designed to be delivered at least once\. Typically, event notifications are delivered in seconds but can sometimes take a minute or longer\. 

## Overview of Amazon S3 Event Notifications<a name="notification-how-to-overview"></a>

 Currently, Amazon S3 can publish notifications for the following events:
+ **New object created events** — Amazon S3 supports multiple APIs to create objects\. You can request notification when only a specific API is used \(for example, `s3:ObjectCreated:Put`\)\. You can also use a wildcard \(for example, `s3:ObjectCreated:*`\) to request notification when an object is created regardless of the API used\. 
+ **Object removal events** — Amazon S3 supports deletes of versioned and unversioned objects\. For information, see [Using versioning in S3 buckets](Versioning.md)\. 

  You can request notification when a non\-versioned object is deleted or a versioned object is permanently deleted by using the `s3:ObjectRemoved:Delete` event type\. Or you can request notification when a delete marker is created for a versioned object by using `s3:ObjectRemoved:DeleteMarkerCreated`\. You can also use a wildcard `s3:ObjectRemoved:*` to request notification anytime an object is deleted\. For information about deleting versioned objects, see [Deleting object versions from a versioning\-enabled bucket](DeletingObjectVersions.md)\. 
+ **Restore object events **— Amazon S3 supports the restoration of objects archived to the S3 Glacier storage classes\. You request to be notified of object restoration completion by using `s3:ObjectRestore:Completed`\. You use `s3:ObjectRestore:Post` to request notification of the initiation of a restore\.
+ **Reduced Redundancy Storage \(RRS\) object lost events** — Amazon S3 sends a notification message when it detects that an object of the RRS storage class has been lost\. 
+ **Replication events** — Amazon S3 sends event notifications for replication configurations that have S3 replication metrics or S3 Replication Time Control \(S3 RTC\) enabled\. You can monitor minute\-by\-minute progress of replication by tracking bytes pending, operations pending, and replication latency\. For information about replication metrics, see [Monitoring progress with replication metrics and Amazon S3 event notifications](replication-metrics.md)\.

For a list of supported event types, see [Supported event types](notification-how-to-event-types-and-destinations.md#supported-notification-event-types)\. 

Amazon S3 supports the following destinations where it can publish events:
+ **Amazon Simple Notification Service \(Amazon SNS\)**

  Amazon SNS is a flexible, fully managed push messaging service\. Using this service, you can push messages to mobile devices or distributed services\. With SNS you can publish a message once, and deliver it one or more times\. Currently Standard SNS is only allowed as an S3 event notification destination, whereas SNS FIFO is not allowed\. For more information about SNS, see [Amazon SNS](http://aws.amazon.com/sns/)\.
+ **Amazon Simple Queue Service \(Amazon SQS\) queue**

  Amazon SQS is a scalable and fully managed message queuing service\. You can use SQS to transmit any volume of data without requiring other services to be always available\. In your notification configuration, you can request that Amazon S3 publish events to an SQS queue\. 

  Currently, Standard SQS queue is only allowed as an Amazon S3 event notification destination, whereas FIFO SQS queue is not allowed\. For more information about Amazon SQS, see [Amazon SQS](http://aws.amazon.com/sqs/)\.
+ **AWS Lambda**

  AWS Lambda is a compute service that makes it easy for you to build applications that respond quickly to new information\. AWS Lambda runs your code in response to events such as image uploads, in\-app activity, website clicks, or outputs from connected devices\. 

  You can use AWS Lambda to extend other AWS services with custom logic, or create your own backend that operates at AWS scale, performance, and security\. With Lambda, you can easily create discrete, event\-driven applications that run only when needed and scale automatically from a few requests per day to thousands per second\. 

  Lambda can run custom code in response to Amazon S3 bucket events\. You upload your custom code to Lambda and create what is called a Lambda function\. When Amazon S3 detects an event of a specific type \(for example, an object created event\), it can publish the event to AWS Lambda and invoke your function in Lambda\. In response, Lambda runs your function\. 

**Warning**  
If your notification writes to the same bucket that triggers the notification, it could cause an execution loop\. For example, if the bucket triggers a Lambda function each time an object is uploaded, and the function uploads an object to the bucket, then the function indirectly triggers itself\. To avoid this, use two buckets, or configure the trigger to only apply to a prefix used for incoming objects\.  
For more information and an example of using Amazon S3 notifications with AWS Lambda, see [Using AWS Lambda with Amazon S3](https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html) in the *AWS Lambda Developer Guide*\. 

For more information about S3 Event Notifications, see the following sections:

**Topics**
+ [Overview of Amazon S3 Event Notifications](#notification-how-to-overview)
+ [Event notification types and destinations](notification-how-to-event-types-and-destinations.md)
+ [Granting permissions to publish event notification messages to a destination](grant-destinations-permissions-to-s3.md)
+ [Enabling event notifications](how-to-enable-disable-notification-intro.md)
+ [Walkthrough: Configuring a bucket for notifications \(SNS topic or SQS queue\)](ways-to-add-notification-config-to-bucket.md)
+ [Configuring event notifications using object key name filtering](notification-how-to-filtering.md)
+ [Event message structure](notification-content-structure.md)