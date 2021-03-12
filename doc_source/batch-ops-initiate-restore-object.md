--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Restore<a name="batch-ops-initiate-restore-object"></a>



S3 Batch Operations can run operations on a list of Amazon S3 objects that you specify, including initiating restore requests for archived objects\. The following objects must be restored with an [S3 Initiate Restore Object](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_S3InitiateRestoreObjectOperation.html) job before they can be accessed in real time:
+ Objects archived in the S3 Glacier or S3 Glacier Deep Archive storage classes
+ Objects archived through the S3 Intelligent\-Tiering storage class in the Archive Access or Deep Archive Access tiers

Using an S3 Initiate Restore Object operation in your S3 Batch Operations job results in a restore request for every object that is specified in the manifest\.

**Important**  
The S3 Initiate Restore Object job only *initiates* the request to restore objects\. S3 Batch Operations reports the job as complete for each object after the request is initiated for that object\. Amazon S3 doesn't update the job or otherwise notify you when the objects have been restored\. However, you can use event notifications to receive notifications when the objects are available in Amazon S3\. For more information, see [Amazon S3 Event Notifications](NotificationHowTo.md)\.

Restoring archived files from the S3 Glacier or S3 Glacier Deep Archive storage classes differs from restoring files from the S3 Intelligent\-Tiering storage class in the Archive Access or Deep Archive Access tiers\.
+ When you restore from S3 Glacier or S3 Glacier Deep Archive, a temporary *copy* of the object is created\. Amazon S3 deletes this copy after `ExpirationInDays` days have elapsed\. After this copy is deleted, you must submit an additional restore request to access it\.
+ When you restore from the S3 Intelligent\-Tiering Archive Access or Deep Archive Access tiers, the object transitions back into the S3 Intelligent\-Tiering Frequent Access tier\. The object automatically transitions into the Archive Access tier after a minimum of 90 consecutive days of no access\. It moves into the Deep Archive Access tier after a minimum of 180 consecutive days of no access\. Do *not * specify the `ExpirationInDays` argument when restoring archived S3 Intelligent\-Tiering objects\. 
+ Batch Operations jobs can operate either on S3 Glacier and S3 Glacier Deep Archive storage class objects or on S3 Intelligent\-Tiering Archive Access and Deep Archive Access storage tier objects\. They can't operate on both types in the same job\. To restore objects of both types, you *must* create separate Batch Operations jobs\. 

To create an S3 Initiate Restore Object job, the following arguments are available:

**ExpirationInDays**  
This argument specifies how long the S3 Glacier or S3 Glacier Deep Archive object remains available in Amazon S3\. Initiate Restore Object jobs that target S3 Glacier and S3 Glacier Deep Archive objects require `ExpirationInDays` set to 1 or greater\.  
Conversely, do not set `ExpirationInDays` when creating S3 Initiate Restore Object operation jobs that target S3 Intelligent\-Tiering Archive Access and Deep Archive Access tier objects\. Objects in S3 Intelligent\-Tiering archive access tiers are not subject to restore expiry, so specifying `ExpirationInDays` results in restore request failure\.

**GlacierJobTier**  
Amazon S3 can restore objects using one of three different retrieval tiers: `EXPEDITED`, `STANDARD`, and `BULK`\. However, the S3 Batch Operations feature supports only the `STANDARD` and `BULK` retrieval tiers\. For more about the differences between retrieval tiers, see [Archive retrieval options](restoring-objects-retrieval-options.md)\. For more information about pricing for each tier, see the **Requests & data retrievals** section on [Amazon S3 pricing](http://aws.amazon.com/s3/pricing/)\.

## Overlapping restores<a name="batch-ops-initiate-restore-object-in-progress"></a>

If your [S3 Initiate Restore Object](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_S3InitiateRestoreObjectOperation.html) job tries to restore an object that is already in the process of being restored, S3 Batch Operations proceeds as follows\.

The restore operation succeeds for the object if either of the following conditions is true:
+ Compared to the restoration request already in progress, this job's `ExpirationInDays` is the same and `GlacierJobTier` is faster\.
+ The previous restoration request has already completed, and the object is currently available\. In this case, Batch Operations updates the expiration date of the restored object to match the `ExpirationInDays` specified in the in\-progress restoration request\.

The restore operation fails for the object if any of the following conditions are true:
+ The restoration request already in progress has not yet completed, and the restoration duration for this job \(specified by `ExpirationInDays`\) is different from the restoration duration that is specified in the in\-progress restoration request\.
+ The restoration tier for this job \(specified by `GlacierJobTier`\) is the same or slower than the restoration tier that is specified in the in\-progress restoration request\.

## Limitations<a name="batch-ops-initiate-restore-object-limitations"></a>

S3 Initiate Restore Object jobs have the following limitations:
+ You must create the job in the same Region as the archived objects\.
+ S3 Batch Operations does not support the `EXPEDITED` retrieval tier\.
+ S3 Batch Operations does not support restoring subsets of S3 Intelligent\-Tiering or S3 Glacier objects\. You must call [RestoreObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_RestoreObject.html) for this purpose\.  

For more information about restoring objects, see [Restoring an archived object](restoring-objects.md)\.