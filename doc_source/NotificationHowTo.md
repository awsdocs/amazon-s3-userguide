--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Configuring Amazon S3 event notifications<a name="NotificationHowTo"></a>

The Amazon S3 notification feature enables you to receive notifications when certain events happen in your bucket\. To enable notifications, you must first add a notification configuration that identifies the events you want Amazon S3 to publish and the destinations where you want Amazon S3 to send the notifications\. You store this configuration in the *notification* subresource that is associated with a bucket\. For more information, see [Bucket configuration options](UsingBucket.md#bucket-config-options-intro)\. Amazon S3 provides an API for you to manage this subresource\. 

**Important**  
Amazon S3 event notifications are designed to be delivered at least once\. Typically, event notifications are delivered in seconds but can sometimes take a minute or longer\.  
If two writes are made to a single non\-versioned object at the same time, it is possible that only a single event notification will be sent\. If you want to ensure that an event notification is sent for every successful write, you can enable versioning on your bucket\. With versioning, every successful write will create a new version of your object and will also send an event notification\.

## Overview of notifications<a name="notification-how-to-overview"></a>

 Currently, Amazon S3 can publish notifications for the following events:
+ **New object created events** — Amazon S3 supports multiple APIs to create objects\. You can request notification when only a specific API is used \(for example, `s3:ObjectCreated:Put`\), or you can use a wildcard \(for example, `s3:ObjectCreated:*`\) to request notification when an object is created regardless of the API used\. 
+ **Object removal events** — Amazon S3 supports deletes of versioned and unversioned objects\. For information, see [Using versioning in S3 buckets](Versioning.md)\. 

  You can request notification when a non\-versioned object is deleted or a versioned object is permanently deleted by using the `s3:ObjectRemoved:Delete` event type\. Or you can request notification when a delete marker is created for a versioned object by using `s3:ObjectRemoved:DeleteMarkerCreated`\. You can also use a wildcard `s3:ObjectRemoved:*` to request notification anytime an object is deleted\. For information about deleting versioned objects, see [Deleting object versions from a versioning\-enabled bucket](DeletingObjectVersions.md)\. 
+ **Restore object events **— Amazon S3 supports the restoration of objects archived to the S3 Glacier storage classes\. You request to be notified of object restoration completion by using `s3:ObjectRestore:Completed`\. You use `s3:ObjectRestore:Post` to request notification of the initiation of a restore\.
+ **Reduced Redundancy Storage \(RRS\) object lost events** — Amazon S3 sends a notification message when it detects that an object of the RRS storage class has been lost\. 
+ **Replication events** — Amazon S3 sends event notifications for replication configurations that have S3 replication metrics or S3 Replication Time Control \(S3 RTC\) enabled\. You can monitor minute\-by\-minute progress of replication by tracking bytes pending, operations pending, and replication latency\. For information about replication metrics, see [Monitoring progress with replication metrics and Amazon S3 event notifications](replication-metrics.md)\.

For a list of supported event types, see [Supported event types](#supported-notification-event-types)\. 

Amazon S3 supports the following destinations where it can publish events:
+ **Amazon Simple Notification Service \(Amazon SNS\)**

  Amazon SNS is a flexible, fully managed push messaging service\. Using this service, you can push messages to mobile devices or distributed services\. With SNS you can publish a message once, and deliver it one or more times\. Currently Standard SNS is only allowed as S3 event notification destination, whereas SNS FIFO is not allowed\.For more information about SNS, see the [Amazon SNS](https://aws.amazon.com/sns/) product detail page\.
+ **Amazon Simple Queue Service \(Amazon SQS\) queue**

  Amazon SQS is a scalable and fully managed message queuing service\. You can use SQS to transmit any volume of data without requiring other services to be always available\. In your notification configuration, you can request that Amazon S3 publish events to an SQS queue\. 

  Currently, Standard SQS queue is only allowed as an Amazon S3 event notification destination, whereas FIFO SQS queue is not allowed\. For more information about Amazon SQS, see the [Amazon SQS](https://aws.amazon.com/sqs/) product detail page\.
+ **AWS Lambda**

  AWS Lambda is a compute service that makes it easy for you to build applications that respond quickly to new information\. AWS Lambda runs your code in response to events such as image uploads, in\-app activity, website clicks, or outputs from connected devices\. 

  You can use AWS Lambda to extend other AWS services with custom logic, or create your own backend that operates at AWS scale, performance, and security\. With AWS Lambda, you can easily create discrete, event\-driven applications that run only when needed and scale automatically from a few requests per day to thousands per second\. 

  AWS Lambda can run custom code in response to Amazon S3 bucket events\. You upload your custom code to AWS Lambda and create what is called a Lambda function\. When Amazon S3 detects an event of a specific type \(for example, an object created event\), it can publish the event to AWS Lambda and invoke your function in Lambda\. In response, AWS Lambda runs your function\. 

**Warning**  
If your notification ends up writing to the bucket that triggers the notification, this could cause an execution loop\. For example, if the bucket triggers a Lambda function each time an object is uploaded, and the function uploads an object to the bucket, then the function indirectly triggers itself\. To avoid this, use two buckets, or configure the trigger to only apply to a prefix used for incoming objects\.  
For more information and an example of using Amazon S3 notifications with AWS Lambda, see [Using AWS Lambda with Amazon S3](https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html) in the *AWS Lambda Developer Guide*\. 

## Notification configuration<a name="event-notification-configuration"></a>

By default, notifications are not enabled for any type of event\. Therefore, initially the *notification* subresource stores an empty configuration\.

```
<NotificationConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/"> 
</NotificationConfiguration>
```

To enable notifications for events of specific types, you replace the XML with the appropriate configuration that identifies the event types you want Amazon S3 to publish and the destination where you want the events published\. For each destination, you add a corresponding XML configuration\. 

**Publish event messages to an SQS queue**  
To set an SQS queue as the notification destination for one or more event types, you add the `QueueConfiguration`\.

```
<NotificationConfiguration>
  <QueueConfiguration>
    <Id>optional-id-string</Id>
    <Queue>sqs-queue-arn</Queue>
    <Event>event-type</Event>
    <Event>event-type</Event>
     ...
  </QueueConfiguration>
   ...
</NotificationConfiguration>
```

**Publish event messages to an SNS topic**  
To set an SNS topic as the notification destination for specific event types, you add the `TopicConfiguration`\.

```
<NotificationConfiguration>
  <TopicConfiguration>
     <Id>optional-id-string</Id>
     <Topic>sns-topic-arn</Topic>
     <Event>event-type</Event>
     <Event>event-type</Event>
      ...
  </TopicConfiguration>
   ...
</NotificationConfiguration>
```

**Invoke the AWS Lambda function and provide an event message as an argument**  
To set a Lambda function as the notification destination for specific event types, you add the `CloudFunctionConfiguration`\.

```
<NotificationConfiguration>
  <CloudFunctionConfiguration>   
     <Id>optional-id-string</Id>   
     <CloudFunction>cloud-function-arn</CloudFunction>        
     <Event>event-type</Event>      
     <Event>event-type</Event>      
      ...  
  </CloudFunctionConfiguration>
   ...
</NotificationConfiguration>
```

**Remove all notifications configured on a bucket**  
To remove all notifications configured on a bucket, you save an empty `<NotificationConfiguration/>` element in the *notification* subresource\. 

When Amazon S3 detects an event of the specific type, it publishes a message with the event information\. For more information, see [Event message structure](notification-content-structure.md)\. 

## Event notification types and destinations<a name="notification-how-to-event-types-and-destinations"></a>

This section describes the event notification types that are supported by Amazon S3 and the type of destinations where the notifications can be published\. 

### Supported event types<a name="supported-notification-event-types"></a>

Amazon S3 can publish events of the following types\. You specify these event types in the notification configuration\.


|  Event types |  Description  | 
| --- | --- | 
|  *s3:TestEvent*  | When a notification is enabled, Amazon S3 publishes a test notification to ensure that the topic exists and that the bucket owner has permission to publish the specified topic\. If enabling the notification fails, you do not receive test notification\.  | 
|  *s3:ObjectCreated:\** *s3:ObjectCreated:Put* *s3:ObjectCreated:Post* *s3:ObjectCreated:Copy* *s3:ObjectCreated:CompleteMultipartUpload*  | Amazon S3 APIs such as PUT, POST, and COPY can create an object\. Using these event types, you can enable notification when an object is created using a specific API, or you can use the *s3:ObjectCreated:\** event type to request notification regardless of the API that was used to create an object\.  You do not receive event notifications from failed operations\.  | 
|  *s3:ObjectRemoved:\** *s3:ObjectRemoved:Delete* *s3:ObjectRemoved:DeleteMarkerCreated*  | By using the *ObjectRemoved* event types, you can enable notification when an object or a batch of objects is removed from a bucket\. You can request notification when an object is deleted or a versioned object is permanently deleted by using the *s3:ObjectRemoved:Delete* event type\. Or you can request notification when a delete marker is created for a versioned object by using *s3:ObjectRemoved:DeleteMarkerCreated*\. For information about deleting versioned objects, see [Deleting object versions from a versioning\-enabled bucket](DeletingObjectVersions.md)\. You can also use a wildcard `s3:ObjectRemoved:*` to request notification anytime an object is deleted\.  You do not receive event notifications from automatic deletes from lifecycle policies or from failed operations\.  | 
|  *s3:ObjectRestore:Post* *s3:ObjectRestore:Completed*  |  Using restore object event types you can receive notifications for initiation and completion when restoring objects from the S3 Glacier storage class\. You use `s3:ObjectRestore:Post` to request notification of object restoration initiation\. You use `s3:ObjectRestore:Completed` to request notification of restoration completion\.   | 
| s3:ReducedRedundancyLostObject | You can use this event type to request Amazon S3 to send a notification message when Amazon S3 detects that an object of the RRS storage class is lost\. | 
| s3:Replication:OperationFailedReplication | You receive this notification event when an object that was eligible for replication using Amazon S3 Replication Time Control failed to replicate\. | 
| s3:Replication:OperationMissedThreshold | You receive this notification event when an object that was eligible for replication using Amazon S3 Replication Time Control exceeded the 15\-minute threshold for replication\. | 
| s3:Replication:OperationReplicatedAfterThreshold | You receive this notification event for an object that was eligible for replication using the Amazon S3 Replication Time Control feature replicated after the 15\-minute threshold\. | 
| s3:Replication:OperationNotTracked | You receive this notification event for an object that was eligible for replication using Amazon S3 Replication Time Control but is no longer tracked by replication metrics\. | 

### Supported destinations<a name="supported-notification-destinations"></a>

Amazon S3 can send event notification messages to the following destinations\. You specify the ARN value of these destinations in the notification configuration\.

You must grant Amazon S3 permissions to post messages to an Amazon SNS topic or an Amazon SQS queue\. You must also grant Amazon S3 permission to invoke an AWS Lambda function on your behalf\. For information about granting these permissions, see [Granting permissions to publish event notification messages to a destination](grant-destinations-permissions-to-s3.md)\. 

#### Amazon SNS topic<a name="amazon-sns-topic"></a>

Amazon Simple Notification Service \(Amazon SNS\) is a web service that coordinates and manages the delivery or sending of messages to subscribing endpoints or clients\. You can use the Amazon SNS console to create an Amazon SNS topic that your notifications can be sent to\. The Amazon SNS topic must be in the same region as your Amazon S3 bucket\. For information about creating an Amazon SNS topic, see [Getting Started](https://docs.aws.amazon.com/sns/latest/dg/sns-getting-started.html) in the *Amazon Simple Notification Service Developer Guide* and the [SNS FAQ](http://aws.amazon.com/sns/faqs/)\.

Before you can use the Amazon SNS topic that you create as an event notification destination, you need the following:
+ The Amazon Resource Name \(ARN\) for the Amazon SNS topic
+ A valid Amazon SNS topic subscription \(the topic subscribers are notified when a message is published to your Amazon SNS topic\)

#### Amazon SQS queue<a name="amazon-sqs-queue"></a>

Amazon Simple Queue Service \(Amazon SQS\) offers reliable and scalable hosted queues for storing messages as they travel between computers\. You can use the Amazon SQS console to create an Amazon SQS queue that your notifications can be sent to\. The Amazon SQS queue must be in the same region as your Amazon S3 bucket\. For information about creating an Amazon SQS queue, see [What is Amazon Simple Queue Service](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html) and [Getting Started with Amazon SQS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-getting-started.html) in the *Amazon Simple Queue Service Developer Guide*\. 

Before you can use the Amazon SQS queue as an event notification destination, you need the following:
+ The Amazon Resource Name \(ARN\) for the Amazon SQS topic

#### Lambda function<a name="lambda-function"></a>

You can use the AWS Lambda console to create a Lambda function that uses the AWS infrastructure to run the code on your behalf\. \. The Lambda function must be in the same region as your S3 bucket\. You must also have the name or the ARN of a Lambda function to set up the Lambda function as a event notification destination\.

**Warning**  
If your notification ends up writing to the bucket that triggers the notification, this could cause an execution loop\. For example, if the bucket triggers a Lambda function each time an object is uploaded, and the function uploads an object to the bucket, then the function indirectly triggers itself\. To avoid this, use two buckets, or configure the trigger to only apply to a prefix used for incoming objects\.  
For more information and an example of using Amazon S3 notifications with AWS Lambda, see [Using AWS Lambda with Amazon S3](https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html) in the *AWS Lambda Developer Guide*\. 

## Enabling event notifications<a name="how-to-enable-disable-notification-intro"></a>

Enabling notifications is a bucket\-level operation; that is, you store notification configuration information in the *notification* subresource associated with a bucket\. After creating or changing the bucket notification configuration, typically you need to wait 5 minutes for the changes to take effect\. An s3:TestEvent occurs when the notification is first enabled\. You can use any of the following methods to manage notification configuration:
+ **Using the Amazon S3 console**

  The console UI enables you to set a notification configuration on a bucket without having to write any code\. For more information, see [Enabling and configuring event notifications using the Amazon S3 console](enable-event-notifications.md)\.
+ **Programmatically using the AWS SDKs**
**Note**  
If you need to, you can also make the Amazon S3 REST API calls directly from your code\. However, this can be cumbersome because it requires you to write code to authenticate your requests\. 

  Internally, both the console and the SDKs call the Amazon S3 REST API to manage *notification* subresources associated with the bucket\. For notification configuration using AWS SDK examples, see [Walkthrough: Configuring a bucket for notifications \(SNS topic or SQS queue\)](ways-to-add-notification-config-to-bucket.md)\.

  Regardless of the method that you use, Amazon S3 stores the notification configuration as XML in the *notification* subresource associated with a bucket\. For information about bucket subresources, see [Bucket configuration options](UsingBucket.md#bucket-config-options-intro)\. 

For more information about event notifications, see the topics below\.

**Topics**
+ [Overview of notifications](#notification-how-to-overview)
+ [Notification configuration](#event-notification-configuration)
+ [Event notification types and destinations](#notification-how-to-event-types-and-destinations)
+ [Granting permissions to publish event notification messages to a destination](grant-destinations-permissions-to-s3.md)
+ [Enabling event notifications](#how-to-enable-disable-notification-intro)
+ [Walkthrough: Configuring a bucket for notifications \(SNS topic or SQS queue\)](ways-to-add-notification-config-to-bucket.md)
+ [Enabling and configuring event notifications using the Amazon S3 console](enable-event-notifications.md)
+ [Configuring event notifications using object key name filtering](notification-how-to-filtering.md)
+ [Event message structure](notification-content-structure.md)