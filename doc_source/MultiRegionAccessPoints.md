# Multi\-Region Access Points in Amazon S3<a name="MultiRegionAccessPoints"></a>

Amazon S3 Multi\-Region Access Points provide a global endpoint that applications can use to fulfill requests from S3 buckets that are located in multiple AWS Regions\. You can use Multi\-Region Access Points to build multi\-Region applications with the same architecture that's used in a single Region, and then run those applications anywhere in the world\. Instead of sending requests over the congested public internet, Multi\-Region Access Points provide built\-in network resilience with acceleration of internet\-based requests to Amazon S3\. Application requests made to a Multi\-Region Access Point global endpoint use [AWS Global Accelerator](https://docs.aws.amazon.com/global-accelerator/latest/dg/) to automatically route over the AWS global network to the closest\-proximity S3 bucket with an active routing status\. 

When you create a Multi\-Region Access Point, you specify a set of AWS Regions where you want to store data to be served through that Multi\-Region Access Point\. You can use [S3 Cross\-Region Replication \(CRR\)](http://aws.amazon.com/s3/features/replication/) to synchronize data among buckets in those Regions\. You can then request or write data through the Multi\-Region Access Point global endpoint\. Amazon S3 automatically serves requests to the replicated dataset from the closest available Region\. Multi\-Region Access Points are also compatible with applications that are running in Amazon virtual private clouds \(VPCs\), including those that are using [AWS PrivateLink for Amazon S3](privatelink-interface-endpoints.md)\. 

The following image is a graphical representation of an Amazon S3 Multi\-Region Access Point in an active\-active configuration\. The graphic shows how Amazon S3 requests are automatically routed to buckets in the closest active AWS Region\.

![\[How requests are routed through an Amazon S3 Multi-Region Access Point to the bucket with the lowest latency in an active-active configuration.\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/MultiRegionAccessPoints.png)

 The following image is a graphical representation of an Amazon S3 Multi\-Region Access Point in an active\-passive configuration\. The graphic shows how you can control Amazon S3 data\-access traffic to fail over between active and passive AWS Regions\.

![\[How manual failover between Regions works with an Amazon S3 Multi-Region Access Point in an active-passive configuration.\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/MultiRegionAccessPointsFailover.png)

To learn more about how to use Multi\-Region Access Points, see [Tutorial: Getting started with Amazon S3 Multi\-Region Access Points](http://aws.amazon.com/getting-started/hands-on/getting-started-with-amazon-s3-multi-region-access-points/?ref=docs_gateway/amazons3/MultiRegionAccessPoints.html)\.

**Topics**
+ [Creating Multi\-Region Access Points](CreatingMultiRegionAccessPoints.md)
+ [Configuring a Multi\-Region Access Point for use with AWS PrivateLink](MultiRegionAccessConfiguration.md)
+ [Making requests through a Multi\-Region Access Point](MultiRegionAccessPointRequests.md)