# Event notification types and destinations<a name="notification-how-to-event-types-and-destinations"></a>

Amazon S3 supports several event notification types and destinations where the notifications can be published\. You can specify the event type and destination when configuring your event notifications\.

**Topics**
+ [Supported event types](#supported-notification-event-types)
+ [Supported event destinations](#supported-notification-destinations)

## Supported event types<a name="supported-notification-event-types"></a>

Amazon S3 can publish events of the following types\. You specify these event types in the notification configuration\.


|  Event types |  Description  | 
| --- | --- | 
|  *s3:TestEvent*  |  When a notification is enabled, Amazon S3 publishes a test notification to ensure that the topic exists and that the bucket owner has permission to publish the specified topic\. If enabling the notification fails, you do not receive a test notification\.  | 
|  *s3:ObjectCreated:\** *s3:ObjectCreated:Put* *s3:ObjectCreated:Post* *s3:ObjectCreated:Copy* *s3:ObjectCreated:CompleteMultipartUpload*  |  Amazon S3 APIs such as PUT, POST, and COPY can create an object\. With these event types, you can enable notifications when an object is created using a specific API\. Or, you can use the *s3:ObjectCreated:\** event type to request notification regardless of the API that was used to create an object\.  You don't receive event notifications from failed operations\. `s3:ObjectCreated:CompleteMultipartUpload` includes objects that are created using [UploadPartCopy](https://docs.aws.amazon.com/AmazonS3/latest/API/API_UploadPartCopy.html) for Copy operations\.  | 
|  *s3:ObjectRemoved:\** *s3:ObjectRemoved:Delete* *s3:ObjectRemoved:DeleteMarkerCreated*  |  By using the *ObjectRemoved* event types, you can enable notification when an object or a batch of objects is removed from a bucket\. You can request notification when an object is deleted or a versioned object is permanently deleted by using the *s3:ObjectRemoved:Delete* event type\. Or you can request notification when a delete marker is created for a versioned object by using *s3:ObjectRemoved:DeleteMarkerCreated*\. For information about deleting versioned objects, see [Deleting object versions from a versioning\-enabled bucket](DeletingObjectVersions.md)\. You can also use a wildcard `s3:ObjectRemoved:*` to request notification anytime an object is deleted\.  You don't receive event notifications from automatic deletes from lifecycle policies or from failed operations\.  | 
|  *s3:ObjectRestore:Post* *s3:ObjectRestore:Completed*  |  With restore object event types, you can receive notifications for initiation and completion when restoring objects from the S3 Glacier storage class\. You use `s3:ObjectRestore:Post` to request notification of object restoration initiation\. You use `s3:ObjectRestore:Completed` to request notification of restoration completion\.   | 
| s3:ReducedRedundancyLostObject | You can use this event type to request a notification message when Amazon S3 detects that an object of the RRS storage class is lost\. | 
| s3:Replication:OperationFailedReplication | You receive this notification event when an object that was eligible for replication using S3 Replication Time Control failed to replicate\. In addition, you receive this notification if you have S3 replication metrics enabled\. | 
| s3:Replication:OperationMissedThreshold | You receive this notification event when an object that was eligible for replication using S3 Replication Time Control exceeds the 15\-minute threshold for replication\. | 
| s3:Replication:OperationReplicatedAfterThreshold | You receive this notification event for an object that was eligible for replication using S3 Replication Time Control replicates after the 15\-minute threshold\. | 
| s3:Replication:OperationNotTracked | You receive this notification event for an object that was eligible for replication using S3 Replication Time Control but is no longer tracked by replication metrics\. | 

## Supported event destinations<a name="supported-notification-destinations"></a>

Amazon S3 can send event notification messages to the following destinations\. You specify the Amazon Resource Name \(ARN\) value of these destinations in the notification configuration\.
+ Amazon Simple Notification Service \(Amazon SNS\) topics
+ Amazon Simple Queue Service \(Amazon SQS\) queues
+ AWS Lambda function

You must grant Amazon S3 permissions to post messages to an Amazon SNS topic or an Amazon SQS queue\. You must also grant Amazon S3 permission to invoke an AWS Lambda function on your behalf\. For information about granting these permissions, see [Granting permissions to publish event notification messages to a destination](grant-destinations-permissions-to-s3.md)\. 

### Amazon SNS topic<a name="amazon-sns-topic"></a>

Amazon SNS coordinates and manages the delivery or sending of messages to subscribing endpoints or clients\. You can use the Amazon SNS console to create an Amazon SNS topic that your notifications can be sent to\. 

The topic must be in the same AWS Region as your Amazon S3 bucket\. For information about creating an Amazon SNS topic, see [Getting started with Amazon SNS](https://docs.aws.amazon.com/sns/latest/dg/sns-getting-started.html) in the *Amazon Simple Notification Service Developer Guide* and the [Amazon SNS FAQ](http://aws.amazon.com/sns/faqs/)\.

Before you can use the Amazon SNS topic that you create as an event notification destination, you need the following:
+ The ARN for the Amazon SNS topic
+ A valid Amazon SNS topic subscription \(the topic subscribers are notified when a message is published to your Amazon SNS topic\)

### Amazon SQS queue<a name="amazon-sqs-queue"></a>

Amazon SQS offers reliable and scalable hosted queues for storing messages as they travel between computers\. You can use the Amazon SQS console to create an Amazon SQS queue that your notifications can be sent to\. 

The Amazon SQS queue must be in the same Region as your Amazon S3 bucket\. For information about creating an Amazon SQS queue, see [What is Amazon Simple Queue Service](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html) and [Getting started with Amazon SQS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-getting-started.html) in the *Amazon Simple Queue Service Developer Guide*\. 

Before you can use the Amazon SQS queue as an event notification destination, you need the following:
+ The ARN for the Amazon SQS queue

### Lambda function<a name="lambda-function"></a>

You can use the AWS Lambda console to create a Lambda function that uses the AWS infrastructure to run the code on your behalf\. The Lambda function must be in the same Region as your S3 bucket\. You must also have the name or the ARN of a Lambda function to set up the Lambda function as an event notification destination\.

**Warning**  
If your notification writes to the same bucket that triggers the notification, it could cause an execution loop\. For example, if the bucket triggers a Lambda function each time an object is uploaded, and the function uploads an object to the bucket, then the function indirectly triggers itself\. To avoid this, use two buckets, or configure the trigger to only apply to a prefix used for incoming objects\.  
For more information and an example of using Amazon S3 notifications with AWS Lambda, see [Using AWS Lambda with Amazon S3](https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html) in the *AWS Lambda Developer Guide*\. 