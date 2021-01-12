--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Working with Amazon S3 on Outposts<a name="WorkingWithS3Outposts"></a>

You can use S3 on Outposts to store and retrieve objects on\-premises for applications that require local data access, data processing, and data residency\. This section describes how to work with bucket management APIs in S3 on Outposts and the requirements for accessing and monitoring your S3 on Outposts\.

**Topics**
+ [Access Amazon S3 on Outposts resources using Amazon Resource Names \(ARNs\)](#S3OutpostsARNs)
+ [Accessing Amazon S3 on Outposts using virtual private cloud \(VPC\) only access points](AccessingS3Outposts.md)
+ [Monitoring Amazon S3 on Outposts](MonitoringS3Outposts.md)

## Access Amazon S3 on Outposts resources using Amazon Resource Names \(ARNs\)<a name="S3OutpostsARNs"></a>

Amazon S3 supports global buckets\. This means that each bucket name must be unique across all AWS Regions within a partition\. In Amazon S3, you can access a bucket using just its name\. However, in S3 on Outposts, bucket names are unique to an Outpost and require the `Outpost-id` along with the bucket name to identify them\.

*Access points* simplify managing data access at scale for shared datasets in S3\. Access points are named network endpoints that are attached to buckets that you can use to perform Amazon S3 object operations, such as `GetObject` and `PutObject`\. With S3 on Outposts, the bucket endpoint and object API endpoint are different\. So unlike buckets in Amazon S3 that can be accessed directly, you must use access points to access any object in an Outposts bucket\. Access points only support virtual\-host\-style addressing\. 

The following shows the Amazon Resource Name \(ARN\) format for S3 on Outposts buckets:

```
arn:aws:s3-outposts:<region>:<account>:outpost/<outpost-id>/bucket/<bucket-name>
```

The following shows the ARN format for S3 on Outposts access points: 

```
arn:aws:s3-outposts:<region>:<account>:outpost/<outpost-id>/accesspoint/<accesspoint-name>
```

The existing bucket management APIs do not support the concept of location beyond Regions\. Thus, you can't use these APIs to create and manage buckets that are scoped to account, Outpost, and Region\. S3 on Outposts hosts a separate endpoint to manage Outposts bucket APIs that is distinct from S3\. This endpoint is `s3-outposts.<region>.amazonaws.com`\. You must create these endpoints to be able to access your Outposts bucket and perform object operations\. This also enables the API model and behaviors to be the same by allowing the same actions to work in S3 and S3 on Outposts\. This is done by signing the bucket and objects using the correct ARNs\.

You must pass ARNs for the API so that Amazon S3 can determine whether the request is for Amazon S3 \(`s3-control.<region>.amazonaws.com`\) or S3 on Outposts \(`s3-outposts.<region>.amazonaws.com`\)\. S3 can then sign and route the request appropriately\. 

Whenever the request is sent to the Amazon S3 control plane, the SDK extracts the components from the ARN and includes an additional header “`x-amz-outpost-id`” with the value of the “`outpost-id`” extracted from the ARN\. The service name from the ARN is used to sign the request before it is routed to the S3 on Outposts endpoint\. This is applicable for all APIs handled by the `s3control` client\. 

**APIs for S3 on Outposts**  
The following table lists the extended APIs for Amazon S3 on Outposts and their changes relative to Amazon S3\.


|  API |  S3 parameter value |  S3 on Outposts parameter value | 
| --- | --- | --- | 
|  `CreateBucket`  |  bucket name  |  name as ARN, outpost\-id  | 
|  `ListRegionalBuckets` \(new API\)  |  NA  |  outpost\-id  | 
|  `DeleteBucket`  |  bucket name  |  name as ARN  | 
|  `DeleteBucketLifecycleConfiguration`  |  bucket name  |  bucket name as ARN  | 
|  `GetBucketLifecycleConfiguration`  |  bucket name  |  bucket name as ARN  | 
|  `PutBucketLifecycleConfiguration`  |  bucket name  |  bucket name as ARN  | 
|  `GetBucketPolicy`  |  bucket name  |  bucket name as ARN  | 
|  `PutBucketPolicy`  |  bucket name  |  bucket name as ARN  | 
|  `DeleteBucketPolicy`  |  bucket name  |  bucket name as ARN  | 
|  `GetBucketTagging`  |  bucket name  |  bucket name as ARN  | 
|  `PutBucketTagging`  |  bucket name  |  bucket name as ARN  | 
|  `DeleteBucketTagging`  |  bucket name  |  bucket name as ARN  | 
|  `CreateAccessPoint`  |  access point name  |  access point name as ARN  | 
|  `DeleteAccessPoint`  |  access point name  |  access point name as ARN  | 
|  `GetAccessPoint`  |  access point name  |  access point name as ARN  | 
|  `GetAccessPoint`  |  access point name  |  access point name as ARN  | 
|  `ListAccessPoints`  |  access point name  |  access point name as ARN  | 
|  `PutAccessPointPolicy`  |  access point name  |  access point name as ARN  | 
|  `GetAccessPointPolicy`  |  access point name  |  access point name as ARN  | 
|  `DeleteAccessPointPolicy`  |  access point name  |  access point name as ARN  | 

For more information about accessing and managing your S3 on Outposts buckets, see the following topics: