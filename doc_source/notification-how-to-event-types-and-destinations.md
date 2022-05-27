# Event notification types and destinations<a name="notification-how-to-event-types-and-destinations"></a>

Amazon S3 supports several event notification types and destinations where the notifications can be published\. You can specify the event type and destination when configuring your event notifications\.

**Topics**
+ [Supported event destinations](#supported-notification-destinations)
+ [Supported event types for SQS, SNS, and Lambda](#supported-notification-event-types)
+ [Supported event types for Amazon EventBridge](#supported-notification-event-types-eventbridge)

## Supported event destinations<a name="supported-notification-destinations"></a>

Amazon S3 can send event notification messages to the following destinations\.
+ Amazon Simple Notification Service \(Amazon SNS\) topics
+ Amazon Simple Queue Service \(Amazon SQS\) queues
+ AWS Lambda
+ Amazon EventBridge

**Note**  
You must grant Amazon S3 permissions to post messages to an Amazon SNS topic or an Amazon SQS queue\. You must also grant Amazon S3 permission to invoke an AWS Lambda function on your behalf\. For instructions on how to grant these permissions, see [Granting permissions to publish event notification messages to a destination](grant-destinations-permissions-to-s3.md)\. 

### Amazon SNS topic<a name="amazon-sns-topic"></a>

Amazon SNS is a flexible, fully managed push messaging service\. You can use this service to push messages to mobile devices or distributed services\. With SNS, you can publish a message once, and deliver it one or more times\. Currently, Standard SNS is only allowed as an S3 event notification destination, whereas SNS FIFO is not allowed\.

Amazon SNS both coordinates and manages sending and delivering messages to subscribing endpoints or clients\. You can use the Amazon SNS console to create an Amazon SNS topic that your notifications can be sent to\. 

The topic must be in the same AWS Region as your Amazon S3 bucket\. For instructions on how to create an Amazon SNS topic, see [Getting started with Amazon SNS](https://docs.aws.amazon.com/sns/latest/dg/sns-getting-started.html) in the *Amazon Simple Notification Service Developer Guide* and the [Amazon SNS FAQ](http://aws.amazon.com/sns/faqs/)\.

Before you can use the Amazon SNS topic that you created as an event notification destination, you need the following:
+ The Amazon Resource Name \(ARN\) for the Amazon SNS topic
+ A valid Amazon SNS topic subscription\. With it, topic subscribers are notified when a message is published to your Amazon SNS topic\.

### Amazon SQS queue<a name="amazon-sqs-queue"></a>

Amazon SQS offers reliable and scalable hosted queues for storing messages as they travel between computers\. You can use Amazon SQS to transmit any volume of data without requiring other services to be always available\. You can use the Amazon SQS console to create an Amazon SQS queue that your notifications can be sent to\. 

The Amazon SQS queue must be in the same AWS Region as your Amazon S3 bucket\. For instructions on how to create an Amazon SQS queue, see [What is Amazon Simple Queue Service](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html) and [Getting started with Amazon SQS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-getting-started.html) in the *Amazon Simple Queue Service Developer Guide*\. 

Before you can use the Amazon SQS queue as an event notification destination, you need the following:
+ The Amazon Resource Name \(ARN\) for the Amazon SQS queue

### Lambda function<a name="lambda-function"></a>

You can use AWS Lambda to extend other AWS services with custom logic, or create your own backend that operates at AWS scale, performance, and security\. With Lambda, you can create discrete, event\-driven applications that run only when needed\. You can also use it to scale these applications automatically from a few requests a day to thousands a second\. 

Lambda can run custom code in response to Amazon S3 bucket events\. You upload your custom code to Lambda and create what's called a Lambda function\. When Amazon S3 detects an event of a specific type, it can publish the event to AWS Lambda and invoke your function in Lambda\. In response, Lambda runs your function\. One event type it might detect, for example, is an object created event\.

You can use the AWS Lambda console to create a Lambda function that uses the AWS infrastructure to run the code on your behalf\. The Lambda function must be in the same Region as your S3 bucket\. You must also have the name or the ARN of a Lambda function to set up the Lambda function as an event notification destination\.

**Warning**  
If your notification writes to the same bucket that triggers the notification, it could cause an execution loop\. For example, if the bucket triggers a Lambda function each time an object is uploaded, and the function uploads an object to the bucket, then the function indirectly triggers itself\. To avoid this, use two buckets, or configure the trigger to only apply to a prefix used for incoming objects\.  
For more information and an example of using Amazon S3 notifications with AWS Lambda, see [Using AWS Lambda with Amazon S3](https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html) in the *AWS Lambda Developer Guide*\. 

### Amazon EventBridge<a name="eventbridge-dest"></a>

Amazon EventBridge is a serverless event bus, which receives events from AWS services\. You can set up rules to match events and deliver them to targets, such as an AWS service or an HTTP endpoint\. For more information, see [What is EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-what-is.html) in the *Amazon EventBridge User Guide*\.

Unlike other destinations, you can either enable or disable events to be delivered to EventBridge for a bucket\. If you enable delivery, all events are sent to EventBridge\. Moreover, you can use EventBridge rules to route events to additional targets\.

## Supported event types for SQS, SNS, and Lambda<a name="supported-notification-event-types"></a>

Amazon S3 can publish events of the following types\. You specify these event types in the notification configuration\.


|  Event types |  Description  | 
| --- | --- | 
|  *s3:TestEvent*  |  When a notification is enabled, Amazon S3 publishes a test notification\. This is to ensure that the topic exists and that the bucket owner has permission to publish the specified topic\. If enabling the notification fails, you don't receive a test notification\.  | 
|  *s3:ObjectCreated:\** *s3:ObjectCreated:Put* *s3:ObjectCreated:Post* *s3:ObjectCreated:Copy* *s3:ObjectCreated:CompleteMultipartUpload*  |  Amazon S3 API operations such as PUT, POST, and COPY can create an object\. With these event types, you can enable notifications when an object is created using a specific API operation\. Alternatively, you can use the `s3:ObjectCreated:*` event type to request notification regardless of the API that was used to create an object\.  You don't receive event notifications from failed operations\. `s3:ObjectCreated:CompleteMultipartUpload` includes objects that are created using [UploadPartCopy](https://docs.aws.amazon.com/AmazonS3/latest/API/API_UploadPartCopy.html) for Copy operations\.  | 
|  *s3:ObjectRemoved:\** *s3:ObjectRemoved:Delete* *s3:ObjectRemoved:DeleteMarkerCreated*  |  By using the *ObjectRemoved* event types, you can enable notification when an object or a batch of objects is removed from a bucket\. You can request notification when an object is deleted or a versioned object is permanently deleted by using the `s3:ObjectRemoved:Delete` event type\. Alternatively, you can request notification when a delete marker is created for a versioned object using `s3:ObjectRemoved:DeleteMarkerCreated`\. For instructions on how to delete versioned objects, see [Deleting object versions from a versioning\-enabled bucket](DeletingObjectVersions.md)\. You can also use a wildcard `s3:ObjectRemoved:*` to request notification anytime an object is deleted\.  These event notifications don't alert you for automatic deletes from Lifecycle policies or from failed operations\.  | 
|  *s3:ObjectRestore:\** *s3:ObjectRestore:Post* *s3:ObjectRestore:Completed* *s3:ObjectRestore:Delete*  |  By using *ObjectRestore* event types, you can receive notifications for event initiation and completion when restoring objects from the S3 Glacier Flexible Retrieval storage class and S3 Glacier Deep Archive storage class\. You can also receive notifications for when the restored copy of an object expires\. The `s3:ObjectRestore:Post` event type notifies you of object restoration initiation\. The `s3:ObjectRestore:Completed` event type notifies you of restoration completion\. The `s3:ObjectRestore:Delete` event type notifies you when the temporary copy of a restored object expires\.  | 
| s3:ReducedRedundancyLostObject | You receive this notification event when Amazon S3 detects that an object of the RRS storage class is lost\. | 
|  *s3:Replication:\** *s3:Replication:OperationFailedReplication* *s3:Replication:OperationMissedThreshold* *s3:Replication:OperationReplicatedAfterThreshold* *s3:Replication:OperationNotTracked*  |  By using the *Replication* event types, you can receive notifications for replication configurations that have S3 replication metrics or S3 Replication Time Control \(S3 RTC\) enabled\. You can monitor the minute\-by\-minute progress of replication events by tracking bytes pending, operations pending, and replication latency\. For information about replication metrics, see [Monitoring progress with replication metrics and Amazon S3 event notifications](replication-metrics.md) The `s3:Replication:OperationFailedReplication` event type notifies you when an object that was eligible for replication failed to replicate\. The `s3:Replication:OperationMissedThreshold` event type notifies you when an object that was eligible for replication exceeds the 15\-minute threshold for replication\. The `s3:Replication:OperationReplicatedAfterThreshold` event type notifies you when an object that was eligible for replication that uses S3 Replication Time Control replicates after the 15\-minute threshold\. The `s3:Replication:OperationNotTracked` event type notifies you when an object that was eligible for replication that uses S3 Replication Time Control but is no longer tracked by replication metrics\.  | 
|  *s3:LifecycleExpiration:\** *s3:LifecycleExpiration:Delete* *s3:LifecycleExpiration:DeleteMarkerCreated*  |  By using the *LifecycleExpiration* event types, you can receive a notification when Amazon S3 deletes an object based on your S3 Lifecycle configuration\. The `s3:LifecycleExpiration:Delete` event type notifies you when an object in an unversioned bucket is deleted\. It also notifies you when an object version is permanently deleted by an S3 Lifecycle configuration\. The `s3:LifecycleExpiration:DeleteMarkerCreated` event type notifies you when S3 Lifecycle creates a delete marker when a current version of an object in versioned bucket is deleted\.   | 
| s3:LifecycleTransition | You receive this notification event when an object is transitioned to another Amazon S3 storage class by an S3 Lifecycle configuration\. | 
| s3:IntelligentTiering | You receive this notification event when an object within the S3 Intelligent\-Tiering storage class moved to the Archive Access tier or Deep Archive Access tier\.  | 
|  *s3:ObjectTagging:\** *s3:ObjectTagging:Put* *s3:ObjectTagging:Delete*  |  By using the *ObjectTagging* event types, you can enable notification when an object tag is added or deleted from an object\. The `s3:ObjectTagging:Put` event type notifies you when a tag is PUT on an object or an existing tag is updated\. The `s3:ObjectTagging:Delete` event type notifies you when a tag is removed from an object\. | 
| s3:ObjectAcl:Put | You receive this notification event when an ACL is PUT on an object or when an existing ACL is changed\. An event is not generated when a request results in no change to an object’s ACL\. | 

## Supported event types for Amazon EventBridge<a name="supported-notification-event-types-eventbridge"></a>

For a list of event types Amazon S3 will send to Amazon EventBridge, see [Using EventBridge](EventBridge.md)