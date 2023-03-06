# Managing S3 Intelligent\-Tiering<a name="intelligent-tiering-managing"></a>

The S3 Intelligent\-Tiering storage class delivers automatic storage cost savings in three low latency and high throughput access tiers\. It also offers optional archive capabilities to help you get the lowest storage costs in the cloud for data that can be accessed in minutes to hours\. The S3 Intelligent\-Tiering storage class supports all Amazon S3 features, including the following:
+ S3 Inventory, for verifying the access tier of objects
+ S3 Replication, for replicating data to any AWS Region
+ S3 Storage Lens, for viewing storage usage and activity metrics
+ Server\-Side Encryption, for object data
+ S3 Object Lock, for preventing accidental deletion
+ AWS PrivateLink, for accessing Amazon S3 through a private endpoint in a VPC

## Identifying which S3 Intelligent\-Tiering access tier objects are stored in<a name="identify-intelligent-tiering-access-tier"></a>

You can use [Amazon S3 Inventory](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-inventory.html) to get a list of your objects and their corresponding metadata, including their S3 Intelligent\-Tiering access tier\. Amazon S3 Inventory provides CSV, ORC, or Parquet output files that list your objects and their corresponding metadata on either a daily or weekly basis for an Amazon S3 bucket or a shared prefix\. \(Shared prefix refers to objects that have names that begin with a common string\.\) 

## Viewing the archive status of an object within S3 Intelligent\-Tiering<a name="identify-archive-status"></a>

You can set up an Amazon S3 event notification to receive notice when an object within the S3 Intelligent\-Tiering storage class has moved to either the Archive Access tier or the Deep Archive Access tier\. For more information, see [Enabling event notifications](https://docs.aws.amazon.com/AmazonS3/latest/userguide/how-to-enable-disable-notification-intro.html)\.

Amazon S3 can publish event notifications to an Amazon Simple Notification Service \(Amazon SNS\) topic, an Amazon Simple Queue Service \(Amazon SQS\) queue, or an AWS Lambda function\. For more information, see [Amazon S3 Event Notifications](NotificationHowTo.md)\.

The following is an example of a message that Amazon S3 sends to publish an `s3:IntelligentTiering` event\. For more information, see [ Event message structure](https://docs.aws.amazon.com/AmazonS3/latest/userguide/notification-content-structure.html)\.

```
 1. {  
 2.    "Records":[  
 3.       {  
 4.          "eventVersion":"2.3",
 5.          "eventSource":"aws:s3",
 6.          "awsRegion":"us-west-2",
 7.          "eventTime":"1970-01-01T00:00:00.000Z",
 8.          "eventName":"IntelligentTiering",
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
30.                "key":"HappyFace.jpg",
31.                "size":1024,
32.                "eTag":"d41d8cd98f00b204e9800998ecf8427e",              
33.             }
34.          },
35.          "intelligentTieringEventData":{
36.             "destinationAccessTier": "ARCHIVE_ACCESS"
37.             }
38.       }
39.    ]
40. }
```

You can also use a [HEAD object request](https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadObject.html) to view an object's archive status\. If an object is stored using the S3 Intelligent\-Tiering storage class and is in one of the archive tiers, the HEAD object response shows the current archive tier\. It does this using the [https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadObject.html#API_HeadObject_ResponseElements](https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadObject.html#API_HeadObject_ResponseElements) header\. 

The following HEAD object request returns the metadata of an object\.

**Example**  

```
HEAD /my-image.jpg HTTP/1.1
Host: bucket.s3.<Region>.amazonaws.com
Date: Wed, 28 Oct 2009 22:32:00 GMT
Authorization: AWS AKIAIOSFODNN7EXAMPLE:02236Q3V0RonhpaBX5sCYVf1bNRuU=
```

HEAD object requests can also be used to monitor the status of a `restore-object` request\. If the archive restoration is in progress, the HEAD object response includes the [https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadObject.html#API_HeadObject_ResponseElements](https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadObject.html#API_HeadObject_ResponseElements) header\. 

The following is a sample HEAD object response showing an object archived using S3 Intelligent\-Tiering with a restore request in progress\.

**Example**  

```
HTTP/1.1 200 OK
x-amz-id-2: FSVaTMjrmBp3Izs1NnwBZeu7M19iI8UbxMbi0A8AirHANJBo+hEftBuiESACOMJp
x-amz-request-id: E5CEFCB143EB505A
Date: Fri, 13 Nov 2020 00:28:38 GMT
Last-Modified: Mon, 15 Oct 2012 21:58:07 GMT
ETag: "1accb31fcf202eba0c0f41fa2f09b4d7"
x-amz-storage-class: 'INTELLIGENT_TIERING'
x-amz-archive-status: 'ARCHIVE_ACCESS'
x-amz-restore: 'ongoing-request="true"'
x-amz-restore-request-date: 'Fri, 13 Nov 2020 00:20:00 GMT'
Accept-Ranges: bytes
Content-Type: binary/octet-stream
Content-Length: 300
Server: AmazonS3
```

## Restoring objects from the S3 Intelligent\-Tiering Archive Access and Deep Archive Access tiers<a name="restore-data-from-int-tier-archive"></a>

To access objects in the S3 Intelligent\-Tiering Archive Access and Deep Archive Access tiers, you must initiate the [ restore request](https://docs.aws.amazon.com/AmazonS3/latest/userguide/restoring-objects.html) and wait until the object is moved into the Frequent Access tier\. For information on archived objects, see [ Working with archived objects](https://docs.aws.amazon.com/AmazonS3/latest/userguide/archived-objects.html)\.

When you restore an object from the Archive Access tier or Deep Archive Access tier, the object moves back into the Frequent Access tier\. Afterwards, if the object isn't accessed for 30 consecutive days, it automatically moves into the Infrequent Access tier\. Then, it moves into the Archive Access tier after a minimum of 90 consecutive days of no access\. It moves into the Deep Archive Access tier after a minimum of 180 consecutive days of no access\. 

There are no retrieval charges in S3 Intelligent\-Tiering\. [Standard and Bulk](https://docs.aws.amazon.com/AmazonS3/latest/userguide/restoring-objects-retrieval-options.html) data retrievals and restore requests are free of charge for both the Archive Access and Deep Archive Access tiers\. Subsequent restore requests called on archived objects that have already been restored are billed as a GET request\.

**Note**  
When restoring an object in the S3 Intelligent\-Tiering archive access tiers, the restore request uses Standard retrieval as the default retrieval option\. You can specify Standard or Bulk retrieval within `GlacierJobParameters`\. You can also specify Expedited retrieval from the Archive Access tier, which is charged at the Expedited request and retrieval rate\.

You can restore an archived object using the Amazon S3 console, the REST API,  and the AWS Command Line Interface \(AWS CLI\)\. 

### Using the S3 console<a name="restoring-int-tier-archive-objects-console"></a>

**To restore an object using the Amazon S3 console\.**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Bucket name** list, choose the name of the bucket that contains the objects that you want to restore\.

1. In the **Objects** list, select one or more of the objects that you are restoring, choose **Actions**, and then choose **Restore from S3 Intelligent\-Tiering Archive Access or Deep Archive Access\.**

1. Choose **Restore**\.
**Note**  
Unlike in S3 Glacier Flexible Retrieval and S3 Glacier Deep Archive restore requests, you do not need to choose the tier you want to upgrade to\. Objects from the S3 Intelligent\-Tiering Archive Access and Deep Archive Access tiers automatically restore to the Frequent Access tier\.

### Using the REST API<a name="restoring-int-tier-archive-objects-rest"></a>

Amazon S3 provides an API operation for you to initiate an archive restoration\. For more information, see [RestoreObject](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOSTrestore.html) in the *Amazon Simple Storage Service API Reference*\.

### Using the AWS CLI<a name="restoring-int-tier-archive-objects-cli"></a>

Use the `restore-object` command to restore objects from the S3 Intelligent\-Tiering Archive Access or Deep Archive Access tiers\.

The following example restores object *dir1/example\.obj* in *awsexamplebucket*\.

```
aws s3api restore-object --bucket awsexamplebucket --key dir1/example.obj --restore-request '{}'
```

You can use the following command to monitor the status of your `restore-object` request\.

```
aws s3api head-object --bucket awsexamplebucket --key dir1/example.obj
```

For more information, see [restore\-object](https://docs.aws.amazon.com/cli/latest/reference/s3api/restore-object.html) in the AWS CLI Command Reference\.

**Note**  
Unlike in the S3 Glacier Flexible Retrieval and S3 Glacier Deep Archive storage classes, restore requests for S3 Intelligent\-Tiering objects don't accept the `days` value\.

### Checking the restore status of an object<a name="int-tiering-archive-restore-status"></a>

You can check the progress of your object's restoration on the **Object overview** page on the Amazon S3 console\. For more information, see [Viewing an object overview in the Amazon S3 console](view-object-overview.md)\. This page will show that the restoration is **In progress**\. You can be notified of object restoration completion by using the `s3:ObjectRestore:Completed` action with the [ Amazon S3 Event Notifications](https://docs.aws.amazon.com/AmazonS3/latest/userguide/how-to-enable-disable-notification-intro.html) feature\.

The following table summarizes archived object retrieval speeds\.


| Storage class or tier | Expedited | Standard | Bulk | 
| --- | --- | --- | --- | 
|  S3 Intelligent\-Tiering Archive Access tier  |  1–5 minutes  |  3–5 hours  |  5–12 hours  | 
|  S3 Intelligent\-Tiering Deep Archive Access tier  |  Not available  |  Within 12 hours  |  Within 48 hours  | 

**Note**  
 [ Expedited retrievals](https://docs.aws.amazon.com/AmazonS3/latest/userguide/restoring-objects-retrieval-options.html) are a premium feature available for the S3 Intelligent\-Tiering Archive Access tier and are charged at the Expedited request and retrieval rate\.   
For information about paying for Amazon S3, see [Amazon S3 Pricing](https://aws.amazon.com/s3/pricing/)\.