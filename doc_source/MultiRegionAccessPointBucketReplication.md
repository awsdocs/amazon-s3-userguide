# Configuring replication for use with Multi\-Region Access Points<a name="MultiRegionAccessPointBucketReplication"></a>

When you make a request to a Multi\-Region Access Point endpoint, Amazon S3 automatically routes the request to the bucket that is closest to you\. Amazon S3 doesn't consider the contents of the request when making this decision\. If you make a request to `GET` an object, your request might be routed to a bucket that doesn't have a copy of this object\. If that happens, you receive an HTTP status code 404 \(Not Found\) error\. For more information about Multi\-Region Access Point request routing, see [Multi\-Region Access Point request routing](MultiRegionAccessPointRequestRouting.md)\.

If you want the Multi\-Region Access Point to be able to retrieve the object regardless of which bucket receives the request, you must configure Amazon S3 Cross\-Region Replication \(CRR\)\. 

 For example, consider a Multi\-Region Access Point with three buckets: 
+ A bucket named `my-bucket-usw2` in the Region `us-west-2` that contains the object `my-image.jpg` 
+ A bucket named `my-bucket-aps1` in the Region `ap-south-1` that contains the object `my-image.jpg` 
+ A bucket named `my-bucket-euc1` in the Region `eu-central-1` that doesn't contain the object `my-image.jpg` 

In this situation, if you make a `GetObject` request for the object `my-image.jpg`, the success of that request depends upon which bucket receives your request\. Because Amazon S3 doesn't consider the contents of the request, it might route your `GetObject` request to the `my-bucket-euc1` bucket if that bucket responds of closest proximity\. Even though your object is in a bucket in the Multi\-Region Access Point, you will get an HTTP 404 Not Found error because the individual bucket that received your request didn't have the object\. 

Enabling Cross\-Region Replication \(CRR\) helps avoid this result\. With appropriate replication rules, the `my-image.jpg` object is copied over to the `my-bucket-euc1` bucket\. Therefore, if Amazon S3 routes your request to that bucket, you can now retrieve the object\. 

Replication works as normal with buckets that are assigned to a Multi\-Region Access Point\. Amazon S3 doesn't perform any special replication handling with buckets that are in Multi\-Region Access Points\. For more information about configuring replication in your buckets, see [Setting up replication](replication-how-setup.md)\.

**Recommendations for using replication with Multi\-Region Access Points**  
For the best replication performance when working with Multi\-Region Access Points, we recommend the following: 
+ Configure S3 Replication Time Control \(S3 RTC\)\. To replicate your data across different Regions within a predictable time frame, you can use S3 RTC\. S3 RTC replicates 99\.99 percent of new objects stored in Amazon S3 within 15 minutes \(backed by a service\-level agreement\)\. For more information, see [Meeting compliance requirements using S3 Replication Time Control \(S3 RTC\)](replication-time-control.md)\. There are additional charges for S3 RTC\. For information, see [Amazon S3 pricing](https://aws.amazon.com/s3/pricing/)\.
+ Use two\-way \(bidirectional\) replication to support keeping buckets synchronized when buckets are updated through the Multi\-Region Access Point\. For more information, see [Create two\-way replication rules for your Multi\-Region Access Point](mrap-create-two-way-replication-rules.md)\.
+ Create cross\-account Multi\-Region Access Points to replicate data to buckets in separate AWS accounts\. This approach provides account\-level separation, so that data can be accessed from and replicated across different accounts in different Regions other than the source bucket\. Setting up cross\-account Multi\-Region Access Points comes at no additional cost\. If you're a bucket owner but don't own the Multi\-Region Access Point, you pay only for data transfer and request costs\. Multi\-Region Access Point owners pay for data routing and internet\-acceleration costs\. For more information, see [Amazon S3 pricing](https://aws.amazon.com/s3/pricing/)\.
+ Enable replica modification sync for each replication rule to also keep metadata changes to your objects in sync\. For more information, see [Enabling replica modification sync](https://docs.aws.amazon.com/AmazonS3/latest/userguide/replication-for-metadata-changes.html#enabling-replication-for-metadata-changes)\.
+ Enable Amazon CloudWatch metrics to [monitor replication events](https://docs.aws.amazon.com/AmazonS3/latest/userguide/replication-metrics.html) events\. CloudWatch metrics fees apply\. For more information, see [Amazon CloudWatch pricing](https://aws.amazon.com/cloudwatch/pricing/)\.

**Topics**
+ [Create one\-way replication rules for your Multi\-Region Access Point](mrap-create-one-way-replication-rules.md)
+ [Create two\-way replication rules for your Multi\-Region Access Point](mrap-create-two-way-replication-rules.md)
+ [View the replication rules for your Multi\-Region Access Point](mrap-view-replication-rules.md)