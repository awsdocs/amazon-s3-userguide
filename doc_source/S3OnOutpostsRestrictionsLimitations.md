# How is Amazon S3 on Outposts different from Amazon S3?<a name="S3OnOutpostsRestrictionsLimitations"></a>

Amazon S3 on Outposts delivers object storage to your on\-premises AWS Outposts environment and helps you to meet local processing, data residency, and demanding performance needs by keeping data close to on\-premises applications\. Using Amazon S3 APIs and features, S3 on Outposts makes it easy to store, secure, tag, and report on, and control access to the data on your Outposts and extend AWS infrastructure to your in\-premises facility for a consistent hybrid experience\.

For more information about how S3 on Outposts is unique, see the following topics\.

**Topics**
+ [S3 on Outposts specifications](#S3OnOutpostsSpecifications)
+ [API operations supported by S3 on Outposts](#S3OnOutpostsAPILimitations)
+ [Amazon S3 features not supported by S3 on Outposts](#S3OnOutpostsFeatureLimitations)
+ [S3 on Outposts network requirements](#S3OnOutpostsConnectivityRestrictions)

## S3 on Outposts specifications<a name="S3OnOutpostsSpecifications"></a>
+ The maximum Outposts bucket size is 50 TB\.
+ The maximum number of Outposts buckets is 100 per AWS account\.
+ Outposts buckets can only be accessed using access points and endpoints\.
+ The maximum number of access points per Outposts bucket is 10\.
+ Access point policies are limited to 20 KB in size\.
+ The Outpost owner can manage access within your organization in AWS Organizations using AWS Resource Access Manager\. All accounts that need access to the Outpost must be within the same organization as the owner account in AWS Organizations\.
+ The S3 on Outposts bucket owner account is always the owner of all objects in the bucket\.
+ Only the S3 on Outposts bucket owner account can perform operations on the bucket\.
+ Object size limitations are consistent with Amazon S3\.
+ All objects stored on S3 on Outposts are stored in the `OUTPOSTS` storage class\.
+ All objects stored in the `OUTPOSTS` storage class are stored using server\-side encryption with Amazon S3 managed encryption keys \(SSE\-S3\) by default\. You can also explicitly choose to store objects using server\-side encryption with customer\-provided encryption keys \(SSE\-C\)\.
+ If there is not enough space to store an object on your Outpost, the API will return an insufficient capacity exception \(ICE\)\. 

## API operations supported by S3 on Outposts<a name="S3OnOutpostsAPILimitations"></a>

For a list of API operations supported by S3 on Outposts, see [Amazon S3 on Outposts APIs](S3OutpostsAPI.md)\.

## Amazon S3 features not supported by S3 on Outposts<a name="S3OnOutpostsFeatureLimitations"></a>

The following Amazon S3 features are currently not supported by Amazon S3 on Outposts\. Any attempts to use them are rejected\.
+ Access control lists \(ACLs\)
+ Access point alias names
+ Cross\-origin resource sharing \(CORS\)
+ Batch operations
+ Inventory reports
+ Changing the default bucket encryption
+ Public buckets
+ MFA \(multi\-factor authentication\) delete
+ Lifecycle transitions \(aside from object deletion and stopping incomplete multipart uploads\)
+ Object Lock legal hold
+ Object Lock retention
+ Object Versioning
+ SSE\-KMS
+ Replication
+ Replication Time Control
+ Amazon CloudWatch request metrics
+ Metrics configuration
+ Transfer acceleration
+ Event Notifications
+ Requester Pays buckets
+ S3 Select
+ Lambda events
+ Server access logging
+ Presigned URLs
+ HTTP POST requests
+ SOAP
+ Website access

## S3 on Outposts network requirements<a name="S3OnOutpostsConnectivityRestrictions"></a>
+ To route requests to an S3 on Outposts access point, you must create and configure an S3 on Outposts endpoint\. The following limits apply to endpoints for S3 on Outposts:
  + Each virtual private cloud \(VPC\) on your AWS Outposts can have one associated endpoint, and you can have up to 100 endpoints per Outpost\.
  + Multiple access points can be mapped to the same endpoint\.
  + Endpoints can be added to only to VPCs with CIDR blocks in the subspaces of the following CIDR ranges:
    + 10\.0\.0\.0/8
    + 172\.16\.0\.0/12
    + 192\.168\.0\.0/16
+ Endpoints to an Outpost can be created only from VPCs that have non\-overlapping CIDR blocks\.
+ An endpoint can be created only from within its Outposts subnet\.
+ The subnet used to create an endpoint must contain four IP addresses for S3 on Outposts to use\.
+ The customer\-owned IP address pool \(CoIP pool\), if specified, must contain four IP addresses for S3 on Outposts to use\.
+ You can create only one endpoint per Outpost per VPC\.