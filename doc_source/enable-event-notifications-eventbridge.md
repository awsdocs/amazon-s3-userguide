# Enabling Amazon EventBridge<a name="enable-event-notifications-eventbridge"></a>

You can enable Amazon EventBridge using the S3 console, AWS Command Line Interface \(AWS CLI\), or Amazon S3 REST API\. 

## Using the S3 console<a name="eventbridge-console"></a>

**To enable EventBridge event delivery in the S3 console\.**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want to enable events for\.

1. Choose **Properties**\.

1. Navigate to the **Event Notifications** section and find the **Amazon EventBridge** subsection\. Choose **Edit**\.

1. Under** Send notifications to Amazon EventBridge for all events in this bucket** choose **On**\.
**Note**  
After you enable EventBridge, it takes around five minutes for the changes to take effect\.

## Using the AWS CLI<a name="eventbridge-cli"></a>

The following example creates a bucket notification configuration for bucket `DOC-EXAMPLE-BUCKET1` with Amazon EventBridge enabled\.

```
aws s3api put-bucket-notification-configuration --bucket DOC-EXAMPLE-BUCKET1 --notification-configuration '{ "EventBridgeConfiguration": {} }'
```

## Using the REST API<a name="eventbridge-api"></a>

You can programmatically enable Amazon EventBridge on a bucket by calling the Amazon S3 REST API\. For more information see, see [PutBucketNotificationConfiguration](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketNotificationConfiguration.html) in the *Amazon Simple Storage Service API Reference*\.

The following example shows the XML used to create a bucket notification configuration with Amazon EventBridge enabled\.

```
<NotificationConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
  <EventBridgeConfiguration>
  </EventBridgeConfiguration>
</NotificationConfiguration>
```

## Creating EventBridge rules<a name="ev-tutorial"></a>

Once enabled you can create Amazon EventBridge rules for certain tasks\. For example, you can send email notifications when an object is created\. For a full tutorial, see [Tutorial: Send a notification when an Amazon S3 object is created](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-s3-object-created-tutorial.html) in the *Amazon EventBridge User Guide*\.