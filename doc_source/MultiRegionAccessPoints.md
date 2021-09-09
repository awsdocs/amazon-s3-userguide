# Multi\-Region Access Points in Amazon S3<a name="MultiRegionAccessPoints"></a>

Amazon S3 Multi\-Region Access Points provide a global endpoint that applications can use to fulfill requests from S3 buckets located in multiple AWS Regions\. You can use Multi\-Region Access Points to build multi\-Region applications with the same simple architecture used in a single Region, and then run those applications anywhere in the world\. Instead of sending requests over the congested public internet, Multi\-Region Access Points provide built\-in network resilience with acceleration of internet\-based requests to Amazon S3\. Application requests made to a Multi\-Region Access Point global endpoint use [AWS Global Accelerator](https://docs.aws.amazon.com/global-accelerator/latest/dg/) to automatically route over the AWS global network to the S3 bucket with the lowest network latency\. 

 When you create a Multi\-Region Access Point, you specify a set of Regions where you want to store data to be served through that Multi\-Region Access Point\. You can use [S3 Cross\-Region Replication \(CRR\)](http://aws.amazon.com/s3/features/replication/) to synchronize data among buckets in those Regions\. You can then request or write data through the Multi\-Region Access Point global endpoint\. Amazon S3 automatically serves the request to the replicated dataset from the available Region over the AWS global network with the lowest latency\. Multi\-Region Access Points are also compatible with applications running in Amazon virtual private clouds \(VPCs\), including those using [AWS PrivateLink for Amazon S3](privatelink-interface-endpoints.md)\. 

 The following is a graphical representation of a Multi\-Region Access Point and how it routes requests to buckets\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/MultiRegionAccessPoints.png)

**Topics**
+ [Creating Multi\-Region Access Points](CreatingMultiRegionAccessPoints.md)
+ [Making requests using a Multi\-Region Access Point](MultiRegionAccessPointRequests.md)
+ [Managing Multi\-Region Access Points](ManagingMultiRegionAccessPoints.md)
+ [Monitoring and logging requests made through a Multi\-Region Access Point to underlying resources](MultiRegionAccessPointMonitoring.md)
+ [Multi\-Region Access Point restrictions and limitations](MultiRegionAccessPointRestrictions.md)