--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Notification configuration<a name="event-notification-configuration"></a>

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