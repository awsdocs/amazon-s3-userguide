--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Configuring Amazon S3 event notifications<a name="NotificationHowTo"></a>

The Amazon S3 notification feature enables you to receive notifications when certain events happen in your bucket\. To enable notifications, you must first add a notification configuration that identifies the events you want Amazon S3 to publish and the destinations where you want Amazon S3 to send the notifications\. You store this configuration in the *notification* subresource that is associated with a bucket\. For more information, see [Bucket configuration options](UsingBucket.md#bucket-config-options-intro)\. Amazon S3 provides an API for you to manage this subresource\. 

**Important**  
Amazon S3 event notifications are designed to be delivered at least once\. Typically, event notifications are delivered in seconds but can sometimes take a minute or longer\.  
If two writes are made to a single non\-versioned object at the same time, it is possible that only a single event notification will be sent\. If you want to ensure that an event notification is sent for every successful write, you can enable versioning on your bucket\. With versioning, every successful write will create a new version of your object and will also send an event notification\.

## Enabling event notifications<a name="how-to-enable-disable-notification-intro"></a>

Enabling notifications is a bucket\-level operation; that is, you store notification configuration information in the *notification* subresource associated with a bucket\. After creating or changing the bucket notification configuration, typically you need to wait 5 minutes for the changes to take effect\. You can use any of the following methods to manage notification configuration:
+ **Using the Amazon S3 console**

  The console UI enables you to set a notification configuration on a bucket without having to write any code\. For more information, see [Enabling and configuring event notifications using the Amazon S3 console](enable-event-notifications.md)\.
+ **Programmatically using the AWS SDKs**
**Note**  
If you need to, you can also make the Amazon S3 REST API calls directly from your code\. However, this can be cumbersome because it requires you to write code to authenticate your requests\. 

  Internally, both the console and the SDKs call the Amazon S3 REST API to manage *notification* subresources associated with the bucket\. For notification configuration using AWS SDK examples, see [Walkthrough: Configuring a bucket for notifications \(SNS topic or SQS queue\)](ways-to-add-notification-config-to-bucket.md)\.

  Regardless of the method that you use, Amazon S3 stores the notification configuration as XML in the *notification* subresource associated with a bucket\. For information about bucket subresources, see [Bucket configuration options](UsingBucket.md#bucket-config-options-intro)\. 

For more information about event notifications, see the topics below\.

**Topics**
+ [Enabling event notifications](#how-to-enable-disable-notification-intro)
+ [Overview of notifications](notification-how-to-overview.md)
+ [Notification configuration](event-notification-configuration.md)
+ [Event notification types and destinations](notification-how-to-event-types-and-destinations.title.md)
+ [Configuring notifications with object key name filtering](notification-how-to-filtering.md)
+ [Granting permissions to publish event notification messages to a destination](grant-destinations-permissions-to-s3.md)
+ [Walkthrough: Configuring a bucket for notifications \(SNS topic or SQS queue\)](ways-to-add-notification-config-to-bucket.md)
+ [Enabling and configuring event notifications using the Amazon S3 console](enable-event-notifications.md)
+ [Event message structure](notification-content-structure.md)
