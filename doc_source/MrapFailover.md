# Amazon S3 Multi\-Region Access Points failover controls<a name="MrapFailover"></a>

With Amazon S3 Multi\-Region Access Point failover controls, you can maintain business continuity during Regional traffic disruptions, while also giving your applications a multi\-Region architecture to fulfill compliance and redundancy needs\. If your Regional traffic gets disrupted, you can use Multi\-Region Access Point failover controls to select which AWS Regions behind an Amazon S3 Multi\-Region Access Point will process data\-access and storage requests\. 

To support failover, you can set up your Multi\-Region Access Point in an active\-passive configuration, with traffic flowing to the active Region during normal conditions, and a passive Region on standby for failover\. 

For example, to perform failover to an AWS Region of your choice, you shift traffic from your primary \(active\) Region to your secondary \(passive\) Region\. In an active\-passive configuration like this, one bucket is active and accepting traffic, while the other bucket is passive and not accepting traffic\. The passive bucket is used for disaster recovery\. When you initiate failover, all traffic \(such as `GET` or `PUT` requests\) is directed to the bucket in the active state \(in one Region\) and away from the bucket in the passive state \(in another Region\)\.

If you have S3 Cross\-Region Replication \(CRR\) enabled with two\-way replication rules, you can keep your buckets synchronized during a failover\. In addition, if you have CRR enabled in an active\-active configuration, Amazon S3 Multi\-Region Access Points can also fetch data from the bucket location of closest proximity, which improves application performance\. 

## AWS Region support<a name="RegionSupport"></a>

With Amazon S3 Multi\-Region Access Points failover controls, your S3 buckets can be in any of the [17 Regions](https://docs.aws.amazon.com/AmazonS3/latest/userguide/MultiRegionAccessPointRestrictions.html) where Multi\-Region Access Points are supported\. You can initiate failover across any two Regions at one time\.

**Note**  
Although failover is initiated between only two Regions at one time, you can separately update the routing statuses for multiple Regions at the same time in your Multi\-Region Access Point\.

The following topics demonstrate how to use and manage Amazon S3 Multi\-Region Access Point failover controls\.

**Topics**
+ [AWS Region support](#RegionSupport)
+ [Amazon S3 Multi\-Region Access Points routing states](FailoverConfiguration.md)
+ [Using Amazon S3 Multi\-Region Access Point failover controls](UsingFailover.md)
+ [Amazon S3 Multi\-Region Access Point failover controls errors](mrap-failover-errors.md)