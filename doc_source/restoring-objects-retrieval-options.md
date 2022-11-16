# Archive retrieval options<a name="restoring-objects-retrieval-options"></a>

The following are the available retrieval options when restoring an archived object in Amazon S3: 
+ **`Expedited`** – Quickly access your data that is stored in the S3 Glacier Flexible Retrieval storage class or S3 Intelligent\-Tiering Archive Access tier when occasional urgent requests for a subset of archives are required\. For all but the largest archived objects \(250 MB\+\), data that is accessed by using expedited retrievals is typically made available within 1–5 minutes\. 

  Provisioned capacity helps ensure that retrieval capacity for expedited retrievals from S3 Glacier Flexible Retrieval is available when you need it\. For more information, see [Provisioned capacity](#restoring-objects-expedited-capacity)\.
+ **`Standard`** – Access any of your archived objects within several hours\. `Standard` is the default option for retrieval requests that do not specify the retrieval option\. Standard retrievals typically finish within 3–5 hours for objects stored in the S3 Glacier Flexible Retrieval storage class or S3 Intelligent\-Tiering Archive Access tier\. These retrievals typically finish within 12 hours for objects stored in the S3 Glacier Deep Archive or S3 Intelligent\-Tiering Deep Archive Access storage class\. Standard retrievals are free for objects that are stored in S3 Intelligent\-Tiering\.
+ **`Bulk`** – The lowest\-cost retrieval option in Amazon S3 Glacier\. With bulk retrievals, you can retrieve large amounts, even petabytes, of data inexpensively\. Bulk retrievals typically finish within 5–12 hours for objects that are stored in the S3 Glacier Flexible Retrieval storage class or S3 Intelligent\-Tiering Archive Access tier\. These retrievals typically finish within 48 hours for objects stored in the S3 Glacier Deep Archive storage class or S3 Intelligent\-Tiering Deep Archive Access tier\. Bulk retrievals are free for objects that are stored in S3 Glacier Flexible Retrieval or S3 Intelligent\-Tiering\.

The following table summarizes the archival retrieval options\. For complete information about pricing, see [Amazon S3 pricing](https://aws.amazon.com/s3/pricing/)\.


| Storage class or tier | Expedited | Standard | Bulk | 
| --- | --- | --- | --- | 
|  S3 Glacier Flexible Retrieval or S3 Intelligent\-Tiering Archive Access  |  1–5 minutes  |  3–5 hours  |  5–12 hours  | 
|  S3 Glacier Deep Archive or S3 Intelligent\-Tiering Deep Archive Access  |  Not available  |  Within 12 hours  |  Within 48 hours  | 

To make an `Expedited`, `Standard`, or `Bulk` retrieval, set the `Tier` request element in the [`POST` Object restore](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOSTrestore.html) REST API request to the option that you want, or the equivalent in the AWS Command Line Interface \(AWS CLI\) or AWS SDKs\. If you purchased provisioned capacity, all expedited retrievals are automatically served through your provisioned capacity\. 

You can restore an archived object programmatically or by using the Amazon S3 console\. Amazon S3 processes only one restore request at a time per object\. You can use both the console and the Amazon S3 API to check the restoration status and to find out when Amazon S3 will delete the restored copy\. 

For more information, see [Restoring an archived object](restoring-objects.md)\. 

## Provisioned capacity<a name="restoring-objects-expedited-capacity"></a>

Provisioned capacity helps ensure that your retrieval capacity for expedited retrievals from S3 Glacier Flexible Retrieval is available when you need it\. Each unit of capacity provides that at least three expedited retrievals can be performed every 5 minutes, and it provides up to 150 megabytes per second \(MBps\) of retrieval throughput\.

If your workload requires highly reliable and predictable access to a subset of your data in minutes, you should purchase provisioned retrieval capacity\. Without provisioned capacity, expedited retrievals might not be accepted during periods of high demand\. If you require access to expedited retrievals under all circumstances, we recommend that you purchase provisioned retrieval capacity\. 

You can purchase provisioned capacity by using the Amazon S3 console, the Amazon S3 Glacier console, the [Purchase Provisioned Capacity](https://docs.aws.amazon.com/amazonglacier/latest/dev/api-PurchaseProvisionedCapacity.html) REST API, the AWS SDKs, or the AWS CLI\. For provisioned capacity pricing information, see [Amazon S3 pricing](https://aws.amazon.com/s3/pricing/)\.

## S3 Glacier restore initiation request rates<a name="restore-initiation-request-rates"></a>

When you initiate restore requests for objects that are stored in the S3 Glacier Flexible Retrieval or S3 Glacier Deep Archive storage class, a retrieval\-requests quota is applied for your AWS account\. If your initiate restore rate exceeds 1,000 requests per second, Amazon S3 Glacier will throttle or reject an otherwise valid request and return a `ThrottlingException` error\. 

Optionally, you can also use S3 Batch Operations to retrieve a large number of objects stored in S3 Glacier Flexible Retrieval or S3 Glacier Deep Archive with a single request\. For more information, see [S3 Batch Operations basics](batch-ops.md#batch-ops-basics)\.

## Upgrading the speed of an in\-progress restore<a name="restoring-objects-upgrade-tier"></a>

Using Amazon S3 restore speed upgrade, you can change the restore speed to a faster speed while the restore request is in progress\. A restore speed upgrade overrides an in\-progress restore request with a faster restore tier\. You cannot slow down an in\-progress restore request\.

To upgrade the speed of an in\-progress restoration, issue another restore request to the same object that sets a new `Tier` request element in the [`POST` Object restore](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOSTrestore.html) REST API, or the equivalent in the AWS CLI or AWS SDKs\. When issuing a request to upgrade the restore tier, you must choose a tier that is faster than the tier that the in\-progress restore request is using\. You must not change any other parameters, such as the `Days` request element\. 

**Note**  
Standard and bulk retrievals for S3 Intelligent\-Tiering are free of charge\. However, subsequent restore requests called on an object that is already being restored are billed as `GET` requests\.

You can be notified when your restore is complete by using Amazon S3 Event Notifications\. For more information, see [Amazon S3 Event Notifications](NotificationHowTo.md)\. Restores are charged at the price of the upgraded tier\. For information about restore pricing, see [Amazon S3 pricing](https://aws.amazon.com/s3/pricing/)\.