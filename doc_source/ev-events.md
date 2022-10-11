# EventBridge event message structure<a name="ev-events"></a>

The notification message that Amazon S3 sends to publish an event is in the JSON format\. When Amazon S3 sends an event to Amazon EventBridge, the following fields are present\.
+ **version** — Currently 0 \(zero\) for all events\.
+ **id** — A Version 4 UUID generated for every event\.
+ **detail\-type** — The type of event that's being sent\. See [Using EventBridge](EventBridge.md) for a list of event types\.
+ **source** — Identifies the service that generated the event\.
+ **account** — The 12\-digit AWS account ID of the bucket owner\.
+ **time** — The time the event occurred\.
+ **region** — Identifies the AWS Region of the bucket\.
+ **resource** — A JSON array that contains the Amazon Resource Name \(ARN\) of the bucket\.
+ **detail** — A JSON object that contains information about the event\. For more information about what can be included in this field, see [Event message detail field](#ev-events-detail)\.

## Event message structure examples<a name="ev-events-list"></a>

The following are examples of some of the Amazon S3 event notification messages that can be sent to Amazon EventBridge\.

### Object created<a name="ev-events-object-created"></a>

```
{
  "version": "0",
  "id": "17793124-05d4-b198-2fde-7ededc63b103",
  "detail-type": "Object Created",
  "source": "aws.s3",
  "account": "111122223333",
  "time": "2021-11-12T00:00:00Z",
  "region": "ca-central-1",
  "resources": [
    "arn:aws:s3:::DOC-EXAMPLE-BUCKET1"
  ],
  "detail": {
    "version": "0",
    "bucket": {
      "name": "DOC-EXAMPLE-BUCKET1"
    },
    "object": {
      "key": "example-key",
      "size": 5,
      "etag": "b1946ac92492d2347c6235b4d2611184",
      "version-id": "IYV3p45BT0ac8hjHg1houSdS1a.Mro8e",
      "sequencer": "617f08299329d189"
    },
    "request-id": "N4N7GDK58NMKJ12R",
    "requester": "123456789012",
    "source-ip-address": "1.2.3.4",
    "reason": "PutObject"
  }
}
```

### Object deleted \(using DeleteObject\)<a name="ev-events-object-deleted"></a>

```
{
  "version": "0",
  "id": "2ee9cc15-d022-99ea-1fb8-1b1bac4850f9",
  "detail-type": "Object Deleted",
  "source": "aws.s3",
  "account": "111122223333",
  "time": "2021-11-12T00:00:00Z",
  "region": "ca-central-1",
  "resources": [
    "arn:aws:s3:::DOC-EXAMPLE-BUCKET1"
  ],
  "detail": {
    "version": "0",
    "bucket": {
      "name": "DOC-EXAMPLE-BUCKET1"
    },
    "object": {
      "key": "example-key",
      "etag": "d41d8cd98f00b204e9800998ecf8427e",
      "version-id": "1QW9g1Z99LUNbvaaYVpW9xDlOLU.qxgF",
      "sequencer": "617f0837b476e463"
    },
    "request-id": "0BH729840619AG5K",
    "requester": "123456789012",
    "source-ip-address": "1.2.3.4",
    "reason": "DeleteObject",
    "deletion-type": "Delete Marker Created"
  }
}
```

### Object deleted \(using lifecycle expiration\)<a name="ev-events-object-deleted-lifecycle"></a>

```
{
  "version": "0",
  "id": "ad1de317-e409-eba2-9552-30113f8d88e3",
  "detail-type": "Object Deleted",
  "source": "aws.s3",
  "account": "111122223333",
  "time": "2021-11-12T00:00:00Z",
  "region": "ca-central-1",
  "resources": [
    "arn:aws:s3:::DOC-EXAMPLE-BUCKET1"
  ],
  "detail": {
    "version": "0",
    "bucket": {
      "name": "DOC-EXAMPLE-BUCKET1"
    },
    "object": {
      "key": "example-key",
      "etag": "d41d8cd98f00b204e9800998ecf8427e",
      "version-id": "mtB0cV.jejK63XkRNceanNMC.qXPWLeK",
      "sequencer": "617b398000000000"
    },
    "request-id": "20EB74C14654DC47",
    "requester": "s3.amazonaws.com",
    "reason": "Lifecycle Expiration",
    "deletion-type": "Delete Marker Created"
  }
}
```

### Object restore completed<a name="ev-events-object-restore-complete"></a>

```
{
  "version": "0",
  "id": "6924de0d-13e2-6bbf-c0c1-b903b753565e",
  "detail-type": "Object Restore Completed",
  "source": "aws.s3",
  "account": "111122223333",
  "time": "2021-11-12T00:00:00Z",
  "region": "ca-central-1",
  "resources": [
    "arn:aws:s3:::DOC-EXAMPLE-BUCKET1"
  ],
  "detail": {
    "version": "0",
    "bucket": {
      "name": "DOC-EXAMPLE-BUCKET1"
    },
    "object": {
      "key": "example-key",
      "size": 5,
      "etag": "b1946ac92492d2347c6235b4d2611184",
      "version-id": "KKsjUC1.6gIjqtvhfg5AdMI0eCePIiT3"
    },
    "request-id": "189F19CB7FB1B6A4",
    "requester": "s3.amazonaws.com",
    "restore-expiry-time": "2021-11-13T00:00:00Z",
    "source-storage-class": "GLACIER"
  }
}
```

## Event message detail field<a name="ev-events-detail"></a>

The detail field contains a JSON object with information about the event\. The following fields may be present in the detail field\.
+ **version** — Currently 0 \(zero\) for all events\.
+ **bucket** — Information about the Amazon S3 bucket involved in the event\.
+ **object** — Information about the Amazon S3 object involved in the event\.
+ **request\-id** — Request ID in S3 response\.
+ **requester** — AWS account ID or AWS service principal of requester\.
+ **source\-ip\-address** — Source IP address of S3 request\. Only present for events triggered by an S3 request\.
+ **reason** — For **Object Created** events, the S3 API used to create the object: [PutObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html), [POST Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOST.html), [CopyObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html), or [CompleteMultipartUpload](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CompleteMultipartUpload.html)\. For **Object Deleted** events, this is set to **DeleteObject** when an object is deleted by an S3 API call, or **Lifecycle Expiration** when an object is deleted by an S3 Lifecycle expiration rule\. For more information, see [Expiring objects](lifecycle-expire-general-considerations.md)\.
+ **deletion\-type** — For **Object Deleted** events, when an unversioned object is deleted, or a versioned object is permanently deleted, this is set to **Permanently Deleted**\. When a delete marker is created for a versioned object, this is set to **Delete Marker Created**\. For more information, see [Deleting object versions from a versioning\-enabled bucket](DeletingObjectVersions.md)\.
+ **restore\-expiry\-time** — For **Object Restore Completed** events, the time when the temporary copy of the object will be deleted from S3\. For more information, see [Working with archived objects](archived-objects.md)\.
+ **source\-storage\-class** — For **Object Restore Initiated** and **Object Restore Completed** events, the storage class of the object being restored\. For more information, see [Working with archived objects](archived-objects.md)\.
+ **destination\-storage\-class** — For **Object Storage Class Changed** events, the new storage class of the object\. For more information, see [Transitioning objects using Amazon S3 Lifecycle](lifecycle-transition-general-considerations.md)\.
+ **destination\-access\-tier** — For **Object Access Tier Changed** events, the new access tier of the object\. For more information, see [Amazon S3 Intelligent\-Tiering](intelligent-tiering.md)\.