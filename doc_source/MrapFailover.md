# Amazon S3 Multi\-Region Access Points failover controls<a name="MrapFailover"></a>

With Amazon S3 Multi\-Region Access Point failover controls, you can maintain business continuity during Regional traffic disruptions, while also giving your applications a multi\-Region architecture to fulfill compliance and redundancy needs\. If your Regional traffic gets disrupted, you can use Multi\-Region Access Point failover controls to select which AWS Regions behind an Amazon S3 Multi\-Region Access Point will process data\-access and storage requests\. 

To support failover, you can set up your Multi\-Region Access Point in an active\-passive configuration, with traffic flowing to the active Region during normal conditions, and a passive Region on standby for failover\. 

For example, to perform failover to an AWS Region of your choice, you shift traffic from your primary \(active\) Region to your secondary \(passive\) Region\. In an active\-passive configuration like this, one bucket is active and accepting traffic, while the other bucket is passive and not accepting traffic\. The passive bucket is used for disaster recovery\. When you initiate failover, all traffic \(such as `GET` or `PUT` requests\) is directed to the bucket in the active state \(in one Region\) and away from the bucket in the passive state \(in another Region\)\.

If you have S3 Cross\-Region Replication \(CRR\) enabled with two\-way replication rules, you can keep your buckets synchronized during a failover\. In addition, if you have CRR enabled in an active\-active configuration, Amazon S3 Multi\-Region Access Points can also fetch data from the bucket location of closest proximity, which improves application performance\. 

## Amazon S3 Multi\-Region Access Points routing states<a name="FailoverConfiguration"></a>

Your Amazon S3 Multi\-Region Access Points failover configuration determines the routing status of the AWS Regions that are used with the Multi\-Region Access Point\. You can configure your Amazon S3 Multi\-Region Access Point to be in an active\-active state or active\-passive state\.
+ **Active\-active** – In an active\-active configuration, all requests are automatically sent to the closest proximity AWS Region in your Multi\-Region Access Point\. After the Multi\-Region Access Point has been configured to be in an active\-active state, all Regions can receive traffic\. If traffic disruption occurs in an active\-active configuration, network traffic will automatically be redirected to one of the active Regions\.
+ **Active\-passive** – In an active\-passive configuration, the active Regions in your Multi\-Region Access Point receive traffic and the passive ones do not\. If you intend to use S3 failover controls to initiate failover in a disaster situation, set up your Multi\-Region Access Points in an active\-passive configuration while you're testing and performing disaster\-recovery planning\.

## AWS Region support<a name="RegionSupport"></a>

With Amazon S3 Multi\-Region Access Points failover controls, your S3 buckets can be in any of the [17 Regions](https://docs.aws.amazon.com/AmazonS3/latest/userguide/MultiRegionAccessPointRestrictions.html) where Multi\-Region Access Points are supported\. You can initiate failover across any two Regions at one time\.

**Note**  
Although failover is initiated between only two Regions at one time, you can separately update the routing statuses for multiple Regions at the same time in your Multi\-Region Access Point\.

The following topics demonstrate how to use and manage Amazon S3 Multi\-Region Access Point failover controls\.

**Topics**
+ [Amazon S3 Multi\-Region Access Points routing states](#FailoverConfiguration)
+ [AWS Region support](#RegionSupport)
+ [Using Amazon S3 Multi\-Region Access Point failover controls](UsingFailover.md)