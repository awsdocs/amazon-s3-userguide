# Configuring Lifecycle event notifications<a name="lifecycle-configure-notification"></a>

You can set up an Amazon S3 event notification to receive notice when Amazon S3 deletes an object or transitions it to another Amazon S3 storage class following an S3 Lifecycle rule\.

By using the *LifecycleExpiration* event types you can receive notifications wheneverAmazon S3 deletes an object based on your S3 Lifecycle configuration\. The `s3:LifecycleExpiration:Delete` event type notifies you when an object in an unversioned bucket is deleted\. It also notifies you when an object version is permanently deleted by an S3 Lifecycle configuration\. The `s3:LifecycleExpiration:DeleteMarkerCreated` event type notifies you when S3 Lifecycle creates a delete marker when a current version of an object in versioned bucket is deleted\. For more information, see [Delete object version](https://docs.aws.amazon.com/AmazonS3/latest/userguide/DeletingObjectVersions.html)\.

By using the *s3:LifecycleTransition* event type, you can receive notification when an object is transitioned from one Amazon S3 storage class to another by an S3 Lifecycle configuration\. 

Amazon S3 can publish event notifications to an Amazon Simple Notification Service \(Amazon SNS\) topic, an Amazon Simple Queue Service \(Amazon SQS\) queue, or an AWS Lambda function\. For more information, see [Amazon S3 Event Notifications](NotificationHowTo.md)\.

For instructions on how to configure Amazon S3 Event Notifications, see [ Enabling event notifications](https://docs.aws.amazon.com/AmazonS3/latest/userguide/how-to-enable-disable-notification-intro.html)\.

The following is an example of a message Amazon S3 sends to publish an *s3:LifecycleExpiration:Delete* event\. For more information, see [ Event message structure](https://docs.aws.amazon.com/AmazonS3/latest/userguide/notification-content-structure.html)\.

```
 1. {  
 2.    "Records":[  
 3.       {  
 4.          "eventVersion":"2.3",
 5.          "eventSource":"aws:s3",
 6.          "awsRegion":"us-west-2",
 7.          "eventTime":"1970-01-01T00:00:00.000Z",
 8.          "eventName":"LifecycleExpiration:Delete",
 9.          "userIdentity":{  
10.             "principalId":"s3.amazonaws.com"
11.          },
12.          "requestParameters":{  
13.             "sourceIPAddress":"s3.amazonaws.com"
14.          },
15.          "responseElements":{  
16.             "x-amz-request-id":"C3D13FE58DE4C810",
17.             "x-amz-id-2":"FMyUVURIY8/IgAtTv8xRjskZQpcIZ9KG4V5Wp6S7S/JRWeUWerMUE5JgHvANOjpD"
18.          },
19.          "s3":{  
20.             "s3SchemaVersion":"1.0",
21.             "configurationId":"testConfigRule",
22.             "bucket":{  
23.                "name":"mybucket",
24.                "ownerIdentity":{  
25.                   "principalId":"A3NL1KOZZKExample"
26.                },
27.                "arn":"arn:aws:s3:::mybucket"
28.             },
29.             "object":{  
30.                "key":"expiration/delete",
31.                "sequencer":"0055AED6DCD90281E5",              
32.             }
33.          }
34.       }
35.    ]
36. }
```

Messages that Amazon S3 sends to publish an *s3:LifecycleTransition* event also includes the following information\.

```
1. "lifecycleEventData":{
2.     "transitionEventData": {
3.         "destinationStorageClass": the destination storage class for the object
4.     }
5. }
```