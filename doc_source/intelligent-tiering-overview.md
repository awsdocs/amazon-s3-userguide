# How S3 Intelligent\-Tiering works<a name="intelligent-tiering-overview"></a>

The S3 Intelligent\-Tiering storage class automatically stores objects in three access tiers\. One tier is optimized for frequent access, one lower\-cost tier is optimized for infrequent access, and another very low\-cost tier is optimized for rarely accessed data\. For a low monthly object monitoring and automation charge, S3 Intelligent\-Tiering monitors access patterns and automatically moves objects to the Infrequent Access tier when they have not been accessed for 30 consecutive days\. After 90 days of no access, the objects are moved to the Archive Instant Access tier without performance impact or operational overhead\.

To get the lowest storage cost on data that can be accessed in minutes to hours, you can choose to activate additional archiving capabilities\. After you activate the optional archive capabilities, S3 Intelligent\-Tiering moves objects that have not been accessed for 90 consecutive days to the Archive Access tier\. After 180 consecutive days of no access, the objects are moved to the Deep Archive Access tier\.

There are no retrieval charges in S3 Intelligent\-Tiering\. If an object in the Infrequent Access tier or Archive Instant Access tier is accessed later, it is automatically moved back to the Frequent Access tier\. Downloading or copying an object through the AWS Management Console or running [GetObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html) or [CopyObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html) will initiate access\. Running a HEAD, LIST, GET object tags, or PUT object tags operation will not constitute access\.

You can configure S3 Intelligent\-Tiering as your default storage class for newly created data by specifying `INTELLIGENT-TIERING` in your [S3 PUT API](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketIntelligentTieringConfiguration.html) request header\. S3 Intelligent\-Tiering is designed for 99\.9% availability and 99\.999999999% durability\.

**Note**  
If the size of an object is less than 128 KB, it is not monitored and is not eligible for automatic tiering\. Smaller objects are always stored in the Frequent Access tier\.

## S3 Intelligent\-Tiering access tiers<a name="intel-tiering-tier-definition"></a>

Frequent Access tier \(automatic\)  
This is the default access tier that any object created or transitioned to S3 Intelligent\-Tiering begins its lifecycle in\. An object remains in this tier as long as it is being accessed\. The Frequent Access tier provides low latency and high throughput performance\.

Infrequent Access tier \(automatic\)  
If an object is not accessed for 30 consecutive days, the object moves to the Infrequent Access tier\. The Infrequent Access tier provides low latency and high throughput performance\.

Archive Instant Access tier \(automatic\)  
If an object is not accessed for 90 consecutive days, the object moves to the Archive Instant Access tier\. The Archive Instant Access tier provides low latency and high throughput performance\.

Archive Access tier \(optional\)  
S3 Intelligent\-Tiering provides you with the option to activate the Archive Access tier for data that can be accessed asynchronously\. After activation, the Archive Access tier automatically archives objects that have not been accessed for a minimum of 90 consecutive days\. You can extend the last access time for archiving to a maximum of 730 days\. The Archive Access tier has the same performance as the [S3 Glacier Flexible Retrieval](https://docs.aws.amazon.com/amazonglacier/latest/dev/introduction.html) storage class\. Standard retrieval times for this access tier can range from 3\-5 hours\.  
Only activate the Archive Access tier for 90 days if you want to bypass the Archive Instant Access tier\. The Archive Access tier delivers slightly lower storage cost with minute to hour retrieval times\. The Archive Instant Access tier delivers millisecond access and high throughput performance\.

Deep Archive Access tier \(optional\)  
S3 Intelligent\-Tiering provides you with the option to activate the Deep Archive Access tier for data that can be accessed asynchronously\. After activation, the Deep Archive Access tier automatically archives objects that have not been accessed for a minimum of 180 consecutive days\. You can extend the last access time for archiving to a maximum of 730 days\. The Deep Archive Access tier has the same performance as the [S3 Glacier Deep Archive](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html#sc-glacier) storage class\. Standard retrieval of objects in this access tier occurs within 12 hours\.

**Note**  
Activate the Archive Access and Deep Archive Access tiers only if your objects can be accessed asynchronously by your application\. If the object that you are retrieving is stored in the Archive Access or Deep Archive Access tiers, first restore the object using **RestoreObject**\. For more information, see [ Restoring objects from the S3 Intelligent\-Tiering Archive Access and Deep Archive Access tiers](intelligent-tiering-managing.md#restore-data-from-int-tier-archive)\.