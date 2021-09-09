# How S3 Intelligent\-Tiering works<a name="intelligent-tiering-overview"></a>

The S3 Intelligent\-Tiering storage class automatically stores objects in two access tiers: one tier that is optimized for frequent access and another lower\-cost tier that is optimized for infrequent access\. For a low monthly object monitoring and automation charge, S3 Intelligent\-Tiering monitors access patterns and automatically moves objects that have not been accessed for 30 consecutive days to the Infrequent Access tier, without performance impact or operational overhead\.

For data that can be accessed asynchronously, you can choose to activate automatic archiving capabilities within the S3 Intelligent\-Tiering storage class\. After you enable automatic archiving, S3 Intelligent\-Tiering will move objects that have not been accessed for 90 consecutive days to the Archive Access tier and after 180 consecutive days of no access to the Deep Archive Access tier\.

There are no retrieval charges in S3 Intelligent\-Tiering\. If an object in the Infrequent Access tier is accessed later, it is automatically moved back to the Frequent Access tier\. S3 Intelligent\-Tiering is the recommended storage class for data with unknown, changing, or unpredictable access patterns, independent of object size or retention period, such as data lakes, data analytics, and new applications\.

You can configure S3 Intelligent\-Tiering as your default storage class for newly created data by specifying `INTELLIGENT-TIERING` in your [S3 PUT API](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketIntelligentTieringConfiguration.html) request header\. S3 Intelligent\-Tiering is designed for 99\.9% availability and 99\.999999999% durability\.

**Note**  
If the size of an object is less than 128 KB, it is not monitored and not eligible for auto\-tiering\. Smaller objects are always stored in the Frequent Access tier\.

## S3 Intelligent\-Tiering access tiers<a name="intel-tiering-tier-definition"></a>

Frequent Access tier \(automatic\)  
This is the default access tier that any object created or transitioned to S3 Intelligent\-Tiering begins its lifecycle in\. An object remains in this tier as long as it is being accessed\. The Frequent Access tier provides low latency and high throughput performance\.

Infrequent Access tier \(automatic\)  
If an object is not accessed for 30 consecutive days, the object moves to the Infrequent Access tier\. The Infrequent Access tier provides low latency and high throughput performance\.

Archive Access tier \(optional\)  
S3 Intelligent\-Tiering provides you with the option to enable the Archive Access tier to automatically archive objects that have not been accessed for a minimum of 90 consecutive days\. You can extend the last access time for archiving to a maximum of 730 days\. The Archive Access tier has the same performance as the [S3 Glacier](https://docs.aws.amazon.com/amazonglacier/latest/dev/introduction.html) storage class\.

Deep Archive Access tier \(optional\)  
S3 Intelligent\-Tiering provides you with the option to enable the Deep Archive Access tier to automatically archive objects that have not been accessed for a minimum of 180 consecutive days\. You can extend the last access time for archiving to a maximum of 730 days\. The Deep Archive Access tier has the same performance as the [S3 Glacier Deep Archive](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html#sc-glacier) storage class\.

**Note**  
Activate the Archive Access and Deep Archive Access tiers only if your objects can be accessed asynchronously by your application\. If the object that you are retrieving is stored in the Archive Access or Deep Archive Access tiers, first restore the object using **RestoreObject**\. For more information, see [ Restoring objects from the S3 Intelligent\-Tiering archive access tiers](intelligent-tiering-managing.md#restore-data-from-int-tier-archive)