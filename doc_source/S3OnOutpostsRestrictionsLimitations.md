# Amazon S3 on Outposts restrictions and limitations<a name="S3OnOutpostsRestrictionsLimitations"></a>

Consider the following restrictions and limitations as you set up Amazon S3 on Outposts\.

**Topics**
+ [Specifications](#S3OnOutpostsSpecifications)
+ [Data consistency model](#S3OnOutpostsDataConsistency)
+ [Supported API operations](#S3OnOutpostsAPILimitations)
+ [Unsupported Amazon S3 features](#S3OnOutpostsFeatureLimitations)
+ [Network restrictions](#S3OnOutpostsConnectivityRestrictions)

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

## S3 on Outposts data consistency model<a name="S3OnOutpostsDataConsistency"></a>

Amazon S3 on Outposts provides read\-after\-write consistency for PUT requests of new objects in your Amazon S3 bucket with one caveat: If you make a HEAD or GET request to a key name before the object is created and then create the object shortly after that, a subsequent GET request might not return the object due to eventual consistency\.

Amazon S3 on Outposts offers eventual consistency for overwrite PUT and DELETE requests in all Regions\.

Updates to a single key are atomic\. For example, if you make a PUT request to an existing key, a subsequent read might return the old data or the updated data, but it doesnt return corrupted or partial data\. With the eventual consistency model, you might observe the following behaviors:
+ A process writes a new object to S3 on Outposts and immediately lists keys within its bucket\. Until the change is fully propagated, the object might not appear in the list\.
+ A process replaces an existing object and immediately tries to read it\. Until the change is fully propagated, S3 on Outposts might return the previous data\.
+ A process deletes an existing object and immediately tries to read it\. Until the deletion is fully propagated, S3 on Outposts might return the deleted data\.
+ A process deletes an existing object and immediately lists keys within its bucket\. Until the deletion is fully propagated, S3 on Outposts might list the deleted object\.

## API operations supported by S3 on Outposts<a name="S3OnOutpostsAPILimitations"></a>

Amazon S3 on Outposts is designed to use the same object APIs as Amazon S3\. Therefore, you can use most of your existing code and many of your existing policies by passing the S3 on Outposts Amazon Resource Name \(ARN\) as your identifier\.

Amazon S3 on Outposts supports the following API operations:
+ `AbortMultipartUpload`
+ `CompleteMultipartUpload`
+ `CopyObject`
+ `CreateMultipartUpload`
+ `DeleteObject`
+ `DeleteObjects`
+ `DeleteObjectTagging`
+ `GetObject`
+ `GetObjectTagging`
+ `HeadObject`
+ `HeadBucket`
+ `ListMultipartUploads`
+ `ListObjects`
+ `ListObjectsV2`
+ `ListParts`
+ `PutObject`
+ `PutObjectTagging`
+ `UploadPart`
+ `UploadPartCopy`

## Amazon S3 features not supported by S3 on Outposts<a name="S3OnOutpostsFeatureLimitations"></a>

The following Amazon S3 features are currently not supported by Amazon S3 on Outposts\. Any attempts to use them are rejected\.
+ Access control lists \(ACLs\)
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

## S3 on Outposts network restrictions<a name="S3OnOutpostsConnectivityRestrictions"></a>
+ To route requests to an S3 on Outposts access point, you must create and configure an S3 on Outposts endpoint\. The following limits apply to endpoints for S3 on Outposts:
  + Each virtual private cloud \(VPC\) on your AWS Outposts can have one associated endpoint, and you can have up to 100 endpoints per Outpost\.
  + Multiple access points can be mapped to the same endpoint\.
  + Endpoints can be added to only to VPCs with CIDR blocks in the subspaces of the following CIDR ranges:
    + 10\.0\.0\.0/8
    + 172\.16\.0\.0/12
    + 192\.168\.0\.0/16
+ Endpoints to an Outpost can be created only from VPCs that have non\-overlapping CIDR blocks\.
+ You can create an endpoint only for VPCs that are associated with only one CIDR block\.
+ An endpoint can be created only from within its Outposts subnet\.
+ The subnet used to create an endpoint must contain four IP addresses for S3 on Outposts to use\.
+ The customer\-owned IP address pool \(CoIP pool\), if specified, must contain four IP addresses for S3 on Outposts to use\.
+ You can create only one endpoint per Outpost per VPC\.