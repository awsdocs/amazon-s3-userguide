# Working with archived objects<a name="archived-objects"></a>

When you archive Amazon S3 objects to the following storage classes or tiers, the objects are not accessible in real time: 
+ The S3 Glacier Flexible Retrieval storage class
+ The S3 Glacier Deep Archive storage class
+ The S3 Intelligent\-Tiering Archive Access tier
+ The S3 Intelligent\-Tiering Deep Archive Access tier

To restore the objects, you must do the following:
+ For objects in the Archive Access and Deep Archive Access tiers, you must initiate the restore request and wait until the object is moved into the Frequent Access tier\. 
+ For objects in the S3 Glacier Flexible Retrieval and S3 Glacier Deep Archive storage classes, you must initiate the restore request and wait until a temporary copy of the object is available\.

For more information about how all Amazon S3 storage classes compare, see [Using Amazon S3 storage classes](storage-class-intro.md)\. 

When you are restoring an object from the S3 Intelligent\-Tiering Archive Access tier or S3 Intelligent\-Tiering Deep Archive Access tier, the object moves back into the S3 Intelligent\-Tiering Frequent Access tier\. If the object is not accessed after 30 consecutive days, it automatically moves into the Infrequent Access tier\. After a minimum of 90 consecutive days of no access, the object moves into the S3 Intelligent\-Tiering Archive Access tier\. If the object is not accessed after a minimum of 180 consecutive days, the object moves into the Deep Archive Access tier\.

**Note**  
Unlike in the S3 Glacier Flexible Retrieval and S3 Glacier Deep Archive storage classes, restore requests for S3 Intelligent\-Tiering objects don't accept the `days` value\. 

When you use S3 Glacier Flexible Retrieval or S3 Glacier Deep Archive, Amazon S3 restores a temporary copy of the object only for the specified duration\. After that, it deletes the restored object copy\. You can modify the expiration period of a restored copy by reissuing a restore request\. In this case, Amazon S3 updates the expiration period relative to the current time\. 

**Note**  
When you restore an archive from S3 Glacier Flexible Retrieval or S3 Glacier Deep Archive, you pay for both the archived object and the copy that you restored temporarily\. For information about pricing, see [Amazon S3 pricing](https://aws.amazon.com/s3/pricing/)\.

Amazon S3 calculates the expiration time of the restored object copy by adding the number of days specified in the restoration request to the time when the requested restoration is completed\. Amazon S3 then rounds the resulting time to the next day at midnight Universal Coordinated Time \(UTC\)\. For example, suppose that a restored object copy was created on October 15, 2012, at 10:30 AM UTC, and the restoration period was specified as 3 days\. In this case, the restored copy expires on October 19, 2012, at 00:00 UTC, at which time Amazon S3 deletes the object copy\. 

If a temporary copy of the restored object is created, the object's storage class remains the same\. \(A [https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectHEAD.html](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectHEAD.html) or [https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGET.html](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGET.html) API operation request returns S3 Glacier Flexible Retrieval or S3 Glacier Deep Archive as the storage class\.\) 

The time it takes a restore job to finish depends on which archive storage class or storage tier you use and which retrieval option you specify: `Expedited` \(only available for S3 Glacier Flexible Retrieval and S3 Intelligent\-Tiering Archive Access\), `Standard`, or `Bulk`\. For more information, see [Archive retrieval options](restoring-objects-retrieval-options.md)\.

You can be notified when your restore is complete by using Amazon S3 Event Notifications\. For more information, see [Amazon S3 Event Notifications](EventNotifications.md)\.

**Using S3 Batch Operations with restore requests**  
To restore more than one Amazon S3 object with a single request, you can use S3 Batch Operations\. You provide S3 Batch Operations with a list of objects to operate on\. S3 Batch Operations calls the respective API operation to perform the specified operation\. A single Batch Operations job can perform the specified operation on billions of objects containing exabytes of data\. 

The S3 Batch Operations feature tracks progress, sends notifications, and stores a detailed completion report of all actions, providing a fully managed, auditable, serverless experience\. You can use S3 Batch Operations through the Amazon S3 console, AWS CLI, AWS SDKs, or REST API\. For more information, see [S3 Batch Operations basics](batch-ops.md#batch-ops-basics)\.

The following sections provide more information about restoring archived objects\.

**Topics**
+ [Archive retrieval options](restoring-objects-retrieval-options.md)
+ [Restoring an archived object](restoring-objects.md)