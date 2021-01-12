--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Working with archived objects<a name="restoring-objects"></a>

When you archive Amazon S3 objects to the S3 Glacier or S3 Glacier Deep Archive, or when objects are archived to the S3 Intelligent\-Tiering Archive Access or Deep Archive Access tiers, the objects are not accessible in real time\. 

For objects in the Archive Access or Deep Archive Access tiers, you must first initiate a restore request, and then wait until the object is moved into the Frequent Access tier\. For objects in the S3 Glacier or S3 Glacier Deep Archive storage classes, you must first initiate a restore request, and then wait until a temporary copy of the object is available\. For more information about how all Amazon S3 storage classes compare, see [Using Amazon S3 storage classes](storage-class-intro.md)\. 

When you are restoring from the S3 Intelligent\-Tiering Archive Access tier or S3 Intelligent\-Tiering Deep Archive Access tier, the object moves back into the S3 Intelligent\-Tiering Frequent Access tier\. Afterwards, if the object is not accessed after 30 consecutive days, it automatically moves into the Infrequent Access tier\. It moves into the S3 Intelligent\-Tiering Archive Access tier after a minimum of 90 consecutive days of no access, and it moves into the Deep Archive Access tier after a minimum of 180 consecutive days of no access\.

**Note**  
Unlike in the S3 Glacier and S3 Glacier Deep Archive storage classes, restore requests for S3 Intelligent\-Tiering objects don't accept the `days` value\. 

When you use S3 Glacier or S3 Glacier Deep Archive, Amazon S3 restores a temporary copy of the object only for the specified duration\. After that, it deletes the restored object copy\. You can modify the expiration period of a restored copy by reissuing a restore\. In this case, Amazon S3 updates the expiration period relative to the current time\. 

**Note**  
When you restore an archive from S3 Glacier or S3 Glacier Deep Archive, you pay for both the archived object and a copy that you restored temporarily \(Reduced Redundancy Storage \[RRS\] or Standard, whichever is the lower\-cost storage in the Region\)\. For information about pricing, see [Amazon S3 pricing](https://aws.amazon.com/s3/pricing/)\.

Amazon S3 calculates the expiration time of the restored object copy by adding the number of days specified in the restoration request to the current time\. It then rounds the resulting time to the next day at midnight Universal Coordinated Time \(UTC\)\. For example, suppose that an object was created on October 15, 2012 10:30 AM UTC, and the restoration period was specified as 3 days\. In this case, the restored copy expires on October 19, 2012 00:00 UTC, at which time Amazon S3 deletes the object copy\. 

If a temporary copy of the restored object is created, the object's storage class remains the same\. \(A [HEAD Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectHEAD.html) or the [GET Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGET.html) API operations request returns S3 Glacier or S3 Glacier Deep Archive as the storage class\.\) 

The time it takes a restore job to finish depends on which archive storage class or storage tier you use and which retrieval option you specify: `Expedited` \(only available for S3 Glacier and S3 Intelligent\-Tiering Archive Access\), `Standard`, or `Bulk`\. 

You can be notified when your restore is complete using Amazon S3 event notifications\. For more information, see [Configuring Amazon S3 event notifications](NotificationHowTo.md)\.

When required, you can restore large segments of the data stored for a secondary copy\. However, keep in mind that S3 Glacier and S3 Glacier Deep Archive storage classes and Archive Access and Deep Archive Access tiers are designed for 35 random restore requests per pebibyte \(PiB\) stored per day\.

**Using batch operations with restore requests**  
To restore more than one Amazon S3 object with a single request, you can use S3 Batch Operations\. You provide S3 Batch Operations with a list of objects to operate on\. S3 Batch Operations calls the respective API to perform the specified operation\. A single Batch Operations job can perform the specified operation on billions of objects containing exabytes of data\. 

The S3 Batch Operations feature tracks progress, sends notifications, and stores a detailed completion report of all actions, providing a fully managed, auditable, serverless experience\. You can use S3 Batch Operations through the AWS Management Console, AWS CLI, AWS SDKs, or REST API\. For more information, see [S3 Batch Operations basics](batch-ops-basics.md)\.

The following sections provide more information about restoring objects\.

**Topics**
+ [Archive retrieval options](restoring-objects-retrieval-options.md)
+ [Restoring an archived object](restoring-objects-console.md)
+ [Querying archived objects](querying-glacier-archives.md)