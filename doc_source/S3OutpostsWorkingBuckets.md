# Working with S3 on Outposts buckets<a name="S3OutpostsWorkingBuckets"></a>

With Amazon S3 on Outposts, you can create S3 buckets on your AWS Outposts and easily store and retrieve objects on premises for applications that require local data access, local data processing, and data residency\. S3 on Outposts provides a new storage class, S3 Outposts \(`OUTPOSTS`\), which uses the Amazon S3 APIs, and is designed to store data durably and redundantly across multiple devices and servers on your AWS Outposts\. You can use the same APIs and features on Outpost buckets as you do on Amazon S3, including access policies, encryption, and tagging\. For more information, see [What is Amazon S3 on Outposts?](S3onOutposts.md)

You communicate with your Outpost buckets by using an access point and endpoint connection over a virtual private cloud \(VPC\)\. To access your S3 on Outposts buckets and objects, you must have an access point for the VPC and an endpoint for the same VPC\. For more information, see [Networking for S3 on Outposts](S3OutpostsNetworking.md)\.

## Buckets<a name="S3OutpostsBuckets"></a>

In S3 on Outposts, bucket names are unique to an Outpost and require the AWS Region code for the Region the Outpost is homed to, AWS account ID, Outpost ID, and the bucket name to identify them\.

```
arn:aws:s3-outposts:region:account-id:outpost/outpost-id/bucket/bucket-name
```

For more information, see [Resource ARNs for S3 on Outposts](S3OutpostsIAM.md#S3OutpostsARN)\.

## Access points<a name="S3OutpostsAP"></a>

Amazon S3 on Outposts supports virtual private cloud \(VPC\)\-only access points as the only means to access your Outposts buckets\. 

Access points simplify managing data access at scale for shared datasets in Amazon S3\. Access points are named network endpoints that are attached to buckets that you can use to perform Amazon S3 object operations, such as `GetObject` and `PutObject`\. With S3 on Outposts, you must use access points to access any object in an Outposts bucket\. Access points support only virtual\-host\-style addressing\.

The following example shows the ARN format that you use for S3 on Outposts access points\. The access point ARN includes the AWS Region code for the Region the Outpost is homed to, AWS account ID, Outpost ID, and access point name\.

```
arn:aws:s3-outposts:region:account-id:outpost/outpost-id/accesspoint/accesspoint-name
```

## Endpoints<a name="S3OutpostsEP"></a>

To route requests to an S3 on Outposts access point, you must create and configure an S3 on Outposts endpoint\. With S3 on Outposts endpoints, you can privately connect your VPC to your Outpost bucket\. S3 on Outposts endpoints are virtual uniform resource identifiers \(URIs\) of the entry point to your S3 on Outposts bucket\. They are horizontally scaled, redundant, and highly available VPC components\.

Each virtual private cloud \(VPC\) on your Outpost can have one associated endpoint, and you can have up to 100 endpoints per Outpost\. You must create these endpoints to be able to access your Outpost buckets and perform object operations\. Creating these endpoints also enables the API model and behaviors to be the same by allowing the same operations to work in S3 and S3 on Outposts\. 

## API operations on S3 on Outposts<a name="S3OutpostsBucketAPIs"></a>

To manage Outpost bucket API operations, S3 on Outposts hosts a separate endpoint that is distinct from the Amazon S3 endpoint\. This endpoint is `s3-outposts.region.amazonaws.com`\. 

To use Amazon S3 API operations, you must sign the bucket and objects using the correct ARN format\. You must pass ARNs to API operations so that Amazon S3 can determine whether the request is for Amazon S3 \(`s3-control.region.amazonaws.com`\) or for S3 on Outposts \(`s3-outposts.region.amazonaws.com`\)\. Based on the ARN format, S3 can then sign and route the request appropriately\. 

Whenever a request is sent to the Amazon S3 control plane, the SDK extracts the components from the ARN and includes the additional header `x-amz-outpost-id`, with the `outpost-id` value extracted from the ARN\. The service name from the ARN is used to sign the request before it is routed to the S3 on Outposts endpoint\. This behavior applies to all API operations handled by the `s3control` client\. 

The following table lists the extended API operations for Amazon S3 on Outposts and their changes relative to Amazon S3\.


|  API |  S3 on Outposts parameter value | 
| --- | --- | 
|  `CreateBucket`  |  Bucket name as ARN, Outpost ID  | 
|  `ListRegionalBuckets`  |  Outpost ID  | 
|  `DeleteBucket`  |  Bucket name as ARN  | 
|  `DeleteBucketLifecycleConfiguration`  |  Bucket name as ARN  | 
|  `GetBucketLifecycleConfiguration`  |  Bucket name as ARN  | 
|  `PutBucketLifecycleConfiguration`  |  Bucket name as ARN  | 
|  `GetBucketPolicy`  |  Bucket name as ARN  | 
|  `PutBucketPolicy`  |  Bucket name as ARN  | 
|  `DeleteBucketPolicy`  |  Bucket name as ARN  | 
|  `GetBucketTagging`  |  Bucket name as ARN  | 
|  `PutBucketTagging`  |  Bucket name as ARN  | 
|  `DeleteBucketTagging`  |  Bucket name as ARN  | 
|  `CreateAccessPoint`  |  Access point name as ARN  | 
|  `DeleteAccessPoint`  |  Access point name as ARN  | 
|  `GetAccessPoint`  |  Access point name as ARN  | 
|  `GetAccessPoint`  |  Access point name as ARN  | 
|  `ListAccessPoints`  |  Access point name as ARN  | 
|  `PutAccessPointPolicy`  |  Access point name as ARN  | 
|  `GetAccessPointPolicy`  |  Access point name as ARN  | 
|  `DeleteAccessPointPolicy`  |  Access point name as ARN  | 

## Creating and managing S3 on Outposts buckets<a name="s3-outposts-creating-managing-buckets"></a>

For more information about creating and managing S3 on Outposts buckets, see the following topics\.

**Topics**
+ [Buckets](#S3OutpostsBuckets)
+ [Access points](#S3OutpostsAP)
+ [Endpoints](#S3OutpostsEP)
+ [API operations on S3 on Outposts](#S3OutpostsBucketAPIs)
+ [Creating and managing S3 on Outposts buckets](#s3-outposts-creating-managing-buckets)
+ [Creating an S3 on Outposts bucket](S3OutpostsCreateBucket.md)
+ [Adding tags for S3 on Outposts buckets](S3OutpostsBucketTags.md)
+ [Managing access to an Amazon S3 on Outposts bucket using a bucket policy](S3OutpostsBucketPolicy.md)
+ [Listing Amazon S3 on Outposts buckets](S3OutpostsListBuckets.md)
+ [Getting an S3 on Outposts bucket by using the AWS CLI and the SDK for Java](S3OutpostsGetBucket.md)
+ [Deleting your Amazon S3 on Outposts bucket](S3OutpostsDeleteBucket.md)
+ [Working with Amazon S3 on Outposts access points](S3OutpostsAccessPoints.md)
+ [Working with Amazon S3 on Outposts endpoints](S3OutpostsEndpointsWorking.md)