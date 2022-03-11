# Networking for S3 on Outposts<a name="S3OutpostsNetworking"></a>

You can use Amazon S3 on Outposts to store and retrieve objects on\-premises for applications that require local data access, data processing, and data residency\. This section describes the networking requirements for accessing S3 on Outposts\.

**Topics**
+ [Choosing your networking access type](#S3OutpostsAccessType)
+ [Accessing your S3 on Outposts buckets and objects](#AccessingS3Outposts)
+ [Cross\-account elastic network interfaces](#S3OutpostsXENI)

## Choosing your networking access type<a name="S3OutpostsAccessType"></a>

You can access S3 on Outposts from within a VPC or from your on\-premises network\. You communicate with your Outpost bucket by using an access point and endpoint connection\. This connection keeps traffic between your VPC and your S3 on Outposts buckets within the AWS network\. When you create an endpoint, you must specify the endpoint access type as either `Private` \(for VPC routing\) or `CustomerOwnedIp` \(for a customer\-owned IP address pool \[CoIP pool\]\)\. 
+ `Private` \(for VPC routing\) – If you don't specify the access type, S3 on Outposts uses `Private` by default\. With the `Private` access type, instances in your VPC don't require public IP addresses to communicate with resources in your Outpost\. You can work with S3 on Outposts from within a VPC\. This type of endpoint is not accessible from your on\-premises network\. 
+ `CustomerOwnedIp` \(for CoIP pool\) – If you don't default to the `Private` access type and choose `CustomerOwnedIp`, you must specify an IP address range\. You can use this access type to work with S3 on Outposts from both your on\-premises network and within a VPC\. When accessing S3 on Outposts within a VPC, your traffic is limited to the bandwidth of the local gateway\.

## Accessing your S3 on Outposts buckets and objects<a name="AccessingS3Outposts"></a>

To access your S3 on Outposts buckets and objects, you must have the following:
+ An access point for the VPC\.
+ An endpoint for the same VPC\.
+ An active connection between your Outpost and your AWS Region\. For more information about how to connect your Outpost to a Region, see [ Outpost connectivity to AWS Regions](https://docs.aws.amazon.com/outposts/latest/userguide/how-outposts-works.html#region-connectivity) in the *AWS Outposts User Guide*\. 

For more information about accessing buckets and objects in S3 on Outposts, see [Working with S3 on Outposts buckets](S3OutpostsWorkingBuckets.md) and [Working with S3 on Outposts objects](S3OutpostsWorkingObjects.md)\.

## Cross\-account elastic network interfaces<a name="S3OutpostsXENI"></a>

S3 on Outposts endpoints are named resources with Amazon Resource Names \(ARNs\)\. When these endpoints are created, AWS Outposts sets up multiple cross\-account elastic network interfaces\. S3 on Outposts cross\-account elastic network interfaces are like other network interfaces with one exception: S3 on Outposts associates the cross\-account elastic network interfaces to Amazon EC2 instances\. 

The S3 on Outposts Domain Name System \(DNS\) load balances your requests over the cross\-account elastic network interface\. S3 on Outposts creates the cross\-account elastic network interface in your AWS account that is visible from the **Network interfaces** pane of the Amazon EC2 console\. 

For endpoints that use the CoIP pool access type, S3 on Outposts allocates and associates IP addresses with the cross\-account elastic network interface from the configured CoIP pool\.