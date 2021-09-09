# Configuring bucket replication for use with Multi\-Region Access Points<a name="MultiRegionAccessPointBucketReplication"></a>

 When you make a request to a Multi\-Region Access Point endpoint, Amazon S3 automatically routes the request to the bucket that responds to the request with the lowest latency\. It does not consider the contents of the request when making this decision\. If you make a request to `GET` an object, your request might be routed to a bucket that does not have a copy of this object\. If that happens, you will receive a 404 error\. If you want the Multi\-Region Access Point to be able to recover the object regardless of which bucket receives the request, you must configure Amazon S3 Cross\-Region Replication\. 

 Consider a Multi\-Region Access Point with three buckets: 
+  A bucket named `my-bucket-usw2` in Region `us-west-2` that contains object `my-image.jpg`\. 
+  A bucket named `my-bucket-aps1` in Region `ap-south-1` that contains object `my-image.jpg`\. 
+  A bucket named `my-bucket-euc1` in Region `eu-central-1` that does not contain an object `my-image.jpg`\. 

 In this situation, if you make a `GetObject` request for the object `my-image.jpg`, the success of that request depends upon which bucket receives your request\. Because Amazon S3 does not consider the contents of the request, it might route your `GetObject` request to the `my-bucket-euc1` bucket if that bucket responds with the lowest latency\. Even though your object is in a bucket in the Multi\-Region Access Point, you will get a 404 error because the individual bucket that received your request did not have the object\. 

 Enabling replication helps mitigate this result\. With appropriate replication rules, the `my-image.jpg` image is copied over to the `my-bucket-euc1` bucket, meaning that you retrieve the object if Amazon S3 routes your request to that bucket\. 

 Replication works as normal with buckets that are assigned to a Multi\-Region Access Point\. Amazon S3 does not perform any special handling with buckets that are in Multi\-Region Access Points\. Amazon S3 provides 1:N and N:N replication options for flexible synchronization among buckets\. For more information about configuring replication in your buckets, see [Setting up replication](replication-how-setup.md)\.

 We do have a few recommendations for you if you want to have the greatest replication performance when working with Multi\-Region Access Points\. First, we recommend configuring S3 Replication Time Control \(S3 RTC\), but be aware that comes with an additional cost\. For more information about S3 Replication Time Control, see [Meeting compliance requirements using S3 Replication Time Control \(S3 RTC\)](replication-time-control.md)\. We also recommend enabling bi\-directional replication to support keeping buckets synchronized when a bucket is updated through the Multi\-Region Access Point\. Finally, you should enable Amazon CloudWatch metrics to monitor the replication events\. 

**Warning**  
 If you use the AWS Management Console to create replication rules from the Multi\-Region Access Point console, any pre\-existing replication configurations on the specified buckets will be replaced\. If you want to add to or modify existing replication configurations instead of replacing them, you can modify the rules using each bucket’s replication configuration page in the console, or by using the AWS CLI, SDKs, or REST API\. For more information about modifying replication configurations, see [Replication configuration](replication-add-config.md)\. 