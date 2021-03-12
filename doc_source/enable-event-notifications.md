--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Enabling and configuring event notifications using the Amazon S3 console<a name="enable-event-notifications"></a>

You can enable certain Amazon S3 bucket events to send a notification message to a destination whenever the events occur\. This section explains how to use the Amazon S3 console to enable event notifications\. For information about using event notifications with the AWS SDKs and the Amazon S3 REST APIs, see [Configuring event notifications programmatically](how-to-enable-disable-notification-intro.md#event-notification-configuration)\. 

**Prerequisites**: Before you can enable event notifications for your bucket, you must set up one of the destination types and then configure permissions\. For more information, see [Supported event destinations](notification-how-to-event-types-and-destinations.md#supported-notification-destinations) and [Granting permissions to publish event notification messages to a destination](grant-destinations-permissions-to-s3.md)\.

**To enable and configure event notifications for an S3 bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want to enable events for\.

1. Choose **Properties**\.

1. Navigate to the **Event Notifications** section and choose **Create event notification**\.

1. In the **General configuration** section, specify descriptive event name for your event notification\. Optionally, you can also specify a prefix and a suffix to limit the notifications to objects with keys ending in the specified characters\.

   1. Enter a description for the **Event name**\.

      If you don't enter a name, a globally unique identifier \(GUID\) is generated and used for the name\. 

   1. To optionally filter event notifications by prefix, enter a **Prefix**\. 

      For example, you can set up a prefix filter so that you receive notifications only when files are added to a specific folder \(for example, `images/`\)\. 

   1. To optionally filter event notifications by suffix, enter a **Suffix**\. 

      For more information, see [Configuring event notifications using object key name filtering](notification-how-to-filtering.md)\. 

1. In the **Event types** section, select one or more event types for which you want to receive notifications\. 

   For a listing of the event types, see [Supported event types](notification-how-to-event-types-and-destinations.md#supported-notification-event-types)\.

1. In the **Destination** section, choose the event notification destination\. 
**Note**  
Before you can publish event notifications, you must grant the Amazon S3 principal the necessary permissions to call the relevant API to publish notifications to a Lambda function, SNS topic, or SQS queue\.

   1. Select the destination type: **Lambda Function**, **SNS Topic**, or **SQS Queue**\.

   1. After you choose your destination type, choose a function, topic, or queue from the list\.

   1. Or, if you prefer to specify an Amazon Resource Name \(ARN\), select **Enter ARN** and enter the ARN\.

   For more information, see [Supported event destinations](notification-how-to-event-types-and-destinations.md#supported-notification-destinations)\.

1. Choose **Save changes**, and Amazon S3 sends a test message to the event notification destination\.