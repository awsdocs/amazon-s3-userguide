# Accessing Amazon S3 on Outposts<a name="WorkingWithS3Outposts"></a>

You can use S3 on Outposts to store and retrieve objects on\-premises for applications that require local data access, data processing, and data residency\. This section describes how to work with bucket management APIs in S3 on Outposts and the requirements for accessing S3 on Outposts\.

**Topics**
+ [Accessing S3 on Outposts resources using ARNs](#S3OutpostsARNs)
+ [Accessing Amazon S3 on Outposts using VPC\-only access points](#AccessingS3Outposts)

## Accessing S3 on Outposts resources using ARNs<a name="S3OutpostsARNs"></a>

Amazon S3 supports global buckets, which means that each bucket name must be unique across all AWS Regions within a partition\. In Amazon S3, you can access a bucket using just its name\. However, in S3 on Outposts, bucket names are unique to an Outpost and require the `Outpost-id` along with the bucket name to identify them\.

*Access points* simplify managing data access at scale for shared datasets in Amazon S3\. Access points are named network endpoints that are attached to buckets that you can use to perform Amazon S3 object operations, such as `GetObject` and `PutObject`\. With S3 on Outposts, the bucket endpoint and object API endpoint are different\. So unlike buckets in Amazon S3 that can be accessed directly, you must use access points to access any object in an Outposts bucket\. Access points only support virtual\-host\-style addressing\. 

The following shows the Amazon Resource Name \(ARN\) format for S3 on Outposts buckets\.

```
arn:aws:s3-outposts:<region>:<account>:outpost/<outpost-id>/bucket/<bucket-name>
```

The following shows the ARN format for S3 on Outposts access points\. 

```
arn:aws:s3-outposts:<region>:<account>:outpost/<outpost-id>/accesspoint/<accesspoint-name>
```

The existing bucket management APIs do not support the concept of location beyond Regions\. Thus, you can't use these APIs to create and manage buckets that are scoped to account, Outpost, and Region\. S3 on Outposts hosts a separate endpoint to manage Outposts bucket APIs that is distinct from S3\. This endpoint is `s3-outposts.<region>.amazonaws.com`\. You must create these endpoints to be able to access your Outposts bucket and perform object operations\. This also enables the API model and behaviors to be the same by allowing the same actions to work in S3 and S3 on Outposts\. This is done by signing the bucket and objects using the correct ARNs\.

You must pass ARNs for the API so that Amazon S3 can determine whether the request is for Amazon S3 \(`s3-control.<region>.amazonaws.com`\) or S3 on Outposts \(`s3-outposts.<region>.amazonaws.com`\)\. S3 can then sign and route the request appropriately\. 

Whenever the request is sent to the Amazon S3 control plane, the SDK extracts the components from the ARN and includes an additional header “`x-amz-outpost-id`” with the value of the “`outpost-id`” extracted from the ARN\. The service name from the ARN is used to sign the request before it is routed to the S3 on Outposts endpoint\. This is applicable for all APIs handled by the `s3control` client\. 

**APIs for S3 on Outposts**  
The following table lists the extended APIs for Amazon S3 on Outposts and their changes relative to Amazon S3\.


|  API | Amazon S3 parameter value |  S3 on Outposts parameter value | 
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
|  `CreateAccessPoint`  |  Access point name  |  Access point name as ARN  | 
|  `DeleteAccessPoint`  |  Access point name  |  Access point name as ARN  | 
|  `GetAccessPoint`  |  Access point name  |  Access point name as ARN  | 
|  `GetAccessPoint`  |  Access point name  |  Access point name as ARN  | 
|  `ListAccessPoints`  |  Access point name  |  Access point name as ARN  | 
|  `PutAccessPointPolicy`  |  Access point name  |  Access point name as ARN  | 
|  `GetAccessPointPolicy`  |  Access point name  |  Access point name as ARN  | 
|  `DeleteAccessPointPolicy`  |  Access point name  |  Access point name as ARN  | 

## Accessing Amazon S3 on Outposts using VPC\-only access points<a name="AccessingS3Outposts"></a>

Amazon S3 on Outposts supports virtual private cloud \(VPC\)\-only access points as the only means to access Outposts buckets\. S3 on Outposts endpoints enable you to privately connect your VPC to your AWS Outposts bucket without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. 

Instances in your VPC don't require public IP addresses to communicate with resources in your Outposts\. This keeps traffic between your VPC and your S3 on Outposts buckets within the Amazon network\. 

S3 on Outposts endpoints are virtual devices\. They are horizontally scaled, redundant, and highly available VPC components\. They allow communication between instances in your VPC and S3 on Outposts without imposing availability risks or bandwidth constraints on your network traffic\.

**Note**  
To access your S3 on Outposts buckets and objects, you must have the following:  
An access point that is created for the VPC\.
An endpoint that exists for the same VPC\.

**Topics**
+ [Managing connections for S3 on Outposts using cross\-account elastic network interfaces](#S3OutpostsXENI)
+ [Permissions required for endpoints](#S3OutpostsClusters)
+ [Encryption options with S3 on Outposts](#S3OutpostsEncryption)

### Managing connections for S3 on Outposts using cross\-account elastic network interfaces<a name="S3OutpostsXENI"></a>

S3 on Outposts endpoints are named resources with proper Amazon Resource Names \(ARNs\)\. When these endpoints are created, AWS Outposts sets up four cross\-account elastic network interfaces \(*cross\-account\-ENI*\)\. cross\-account\-ENI are like other elastic network interfaces with one exception: S3 on Outposts attaches the cross\-account\-ENI to instances that it runs in the service account and has a presence in your VPC\. S3 on Outposts DNS load balances your requests over the cross\-account\-ENI\. S3 on Outposts creates the cross\-account\-ENI in your account that is visible from the elastic network interface console\.

### Permissions required for endpoints<a name="S3OutpostsClusters"></a>

To attach the cross\-account elastic network interfaces \(cross\-account\-ENI\) to cluster accounts, S3 on Outposts must also modify these network interfaces to use them with the account ID of the cluster account\. Because of CIDR restrictions, each elastic network interface is unique and on a unique IP\. The source VPC for the IP and ENI ID is recorded and is associated with the cluster ID\. 

### Encryption options with S3 on Outposts<a name="S3OutpostsEncryption"></a>

 By default, all data stored in S3 on Outposts is encrypted using server\-side encryption with Amazon S3 managed encryption keys \(SSE\-S3\)\. You can optionally use server\-side encryption with customer\-provided encryption keys \(SSE\-C\) by specifying an encryption key as part of your object API requests\. Server\-side encryption encrypts only the object data, not object metadata\. 