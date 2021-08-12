# Accessing Amazon S3 on Outposts<a name="WorkingWithS3Outposts"></a>

You can use S3 on Outposts to store and retrieve objects on\-premises for applications that require local data access, data processing, and data residency\. This section describes how to work with bucket management API operations in S3 on Outposts and the requirements for accessing S3 on Outposts\.

**Topics**
+ [Accessing S3 on Outposts resources using ARNs](#S3OutpostsARNs)
+ [Accessing Amazon S3 on Outposts using VPC\-only access points](#AccessingS3Outposts)
+ [Managing connections for S3 on Outposts using cross\-account elastic network interfaces](#S3OutpostsXENI)
+ [Permissions required for S3 on Outposts endpoints](#S3OutpostsEndpointPermissions)
+ [Encryption options with S3 on Outposts](#S3OutpostsEncryption)

## Accessing S3 on Outposts resources using ARNs<a name="S3OutpostsARNs"></a>

Amazon S3 supports global buckets, which means that each bucket name must be unique across all AWS accounts in all the AWS Regions within a partition\. A partition is a grouping of Regions\. AWS currently has three partitions: `aws` \(Standard Regions\), `aws-cn` \(China Regions\), and `aws-us-gov` \(AWS GovCloud \(US\) Regions\)\. In Amazon S3, you can access a bucket using only its name\. However, in S3 on Outposts, bucket names are unique to an Outpost and require the `Outpost-id` along with the bucket name to identify them\.

*Access points* simplify managing data access at scale for shared datasets in Amazon S3\. Access points are named network endpoints that are attached to buckets that you can use to perform Amazon S3 object operations, such as `GetObject` and `PutObject`\. With S3 on Outposts, the bucket endpoint and object API endpoint are different\. Therefore, unlike buckets in Amazon S3 that you can access directly, you must use access points to access any object in an Outposts bucket\. Access points support only virtual\-host\-style addressing\. 

The following example shows the Amazon Resource Name \(ARN\) format for S3 on Outposts buckets\.

```
arn:aws:s3-outposts:region:account-id:outpost/outpost-id/bucket/bucket-name
```

The following example shows the ARN format for S3 on Outposts access points\. 

```
arn:aws:s3-outposts:region:account-id:outpost/outpost-id/accesspoint/accesspoint-name
```

The existing bucket management API operations do not support the concept of location beyond Regions\. Thus, you can't use these API operations to create and manage buckets that are scoped to account, Outpost, and Region\. To manage Outposts bucket API operations, S3 on Outposts hosts a separate endpoint that is distinct from the Amazon S3 endpoint\. This endpoint is `s3-outposts.region.amazonaws.com`\. 

To route requests to an S3 on Outposts access point, you must create and configure an S3 on Outposts endpoint\. Each virtual private cloud \(VPC\) on your AWS Outposts can have one associated endpoint, and you can have up to 100 endpoints per Outpost\. You must create these endpoints to be able to access your Outposts buckets and perform object operations\. Creating these endpoints also enables the API model and behaviors to be the same by allowing the same operations to work in S3 and S3 on Outposts\. To use the same API operations, you must sign the bucket and objects using the correct ARN format\.

You must pass ARNs for the API so that Amazon S3 can determine whether the request is for Amazon S3 \(`s3-control.region.amazonaws.com`\) or for S3 on Outposts \(`s3-outposts.region.amazonaws.com`\)\. Based on the ARN format, S3 can then sign and route the request appropriately\. 

Whenever a request is sent to the Amazon S3 control plane, the SDK extracts the components from the ARN and includes the additional header `x-amz-outpost-id` ,with the `outpost-id` value extracted from the ARN\. The service name from the ARN is used to sign the request before it is routed to the S3 on Outposts endpoint\. This behavior applies to all API operations handled by the `s3control` client\. 

**API operations for S3 on Outposts**  
The following table lists the extended API operations for Amazon S3 on Outposts and their changes relative to Amazon S3\.


|  API | Amazon S3 parameter value |  S3 on Outposts parameter value | 
| --- | --- | --- | 
|  `CreateBucket`  |  Bucket name  |  Bucket name as ARN, Outpost ID  | 
|  `ListRegionalBuckets` \(new API\)  |  NA  |  Outpost ID  | 
|  `DeleteBucket`  |  Bucket name  |  Bucket name as ARN  | 
|  `DeleteBucketLifecycleConfiguration`  |  Bucket name  |  Bucket name as ARN  | 
|  `GetBucketLifecycleConfiguration`  |  Bucket name  |  Bucket name as ARN  | 
|  `PutBucketLifecycleConfiguration`  |  Bucket name  |  Bucket name as ARN  | 
|  `GetBucketPolicy`  |  Bucket name  |  Bucket name as ARN  | 
|  `PutBucketPolicy`  |  Bucket name  |  Bucket name as ARN  | 
|  `DeleteBucketPolicy`  |  Bucket name  |  Bucket name as ARN  | 
|  `GetBucketTagging`  |  Bucket name  |  Bucket name as ARN  | 
|  `PutBucketTagging`  |  Bucket name  |  Bucket name as ARN  | 
|  `DeleteBucketTagging`  |  Bucket name  |  Bucket name as ARN  | 
|  `CreateAccessPoint`  |  Access point name  |  Access point name as ARN  | 
|  `DeleteAccessPoint`  |  Access point name  |  Access point name as ARN  | 
|  `GetAccessPoint`  |  Access point name  |  Access point name as ARN  | 
|  `GetAccessPoint`  |  Access point name  |  Access point name as ARN  | 
|  `ListAccessPoints`  |  Access point name  |  Access point name as ARN  | 
|  `PutAccessPointPolicy`  |  Access point name  |  Access point name as ARN  | 
|  `GetAccessPointPolicy`  |  Access point name  |  Access point name as ARN  | 
|  `DeleteAccessPointPolicy`  |  Access point name  |  Access point name as ARN  | 

## Accessing Amazon S3 on Outposts using VPC\-only access points<a name="AccessingS3Outposts"></a>

Amazon S3 on Outposts supports virtual private cloud \(VPC\)\-only access points as the only means to access Outposts buckets\. With S3 on Outposts endpoints\. you can privately connect your VPC to your Outposts bucket without requiring an internet gateway, network address translation \(NAT\) device, virtual private network \(VPN\) connection, or AWS Direct Connect connection\. 

Instances in your VPC don't require public IP addresses to communicate with resources in your Outposts\. This keeps traffic between your VPC and your S3 on Outposts buckets within the AWS network\. 

S3 on Outposts endpoints are virtual uniform resource identifiers \(URIs\) of the entry point to your S3 on Outposts bucket\. They are horizontally scaled, redundant, and highly available VPC components\. Outpost endpoints are the equivalent of [Outposts local gateway endpoints](https://docs.aws.amazon.com/outposts/latest/userguide/outposts-local-gateways.html) and [AWS PrivateLink for Amazon S3](privatelink-interface-endpoints.md)\. Outpost endpoints allow communication between the instances in your VPC and your on\-premises network and S3 on Outposts without imposing availability risks or bandwidth constraints on your network traffic\. For more information about endpoints, see [AWS service endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html)\.

**Note**  
To access your S3 on Outposts buckets and objects, you must have the following:  
An access point for the VPC
An endpoint for the same VPC

S3 on Outposts endpoints provide the networking layer for S3 access points in the same way that the VPC endpoints used by S3 provide the networking layer for their access points\. S3 on Outposts endpoints work without the need for any additional networking or proxy changes after creation\. 

You can access S3 on Outposts from within a VPC or from your on\-premises network\. There are two access types for an S3 on Outposts endpoint: 
+ **Private** — You can use this access type to work with S3 on Outposts from within a VPC\. This type of endpoint is not accessible from your on\-premises network\. 
+ **Customer\-owned IP address pool \(CoIP pool\)** — You can use this access type to work with S3 on Outposts from your on\-premises network\. You can also use this access type to work with S3 on Outposts from within a VPC, but your traffic will be limited to the bandwidth of the local gateway\.

When creating an endpoint, you must specify the endpoint access type between `Private` \(for VPC routing\) and `CustomerOwnedIp` \(for CoIP pool\)\. If you don't specify the access type, `Private` is used by default\.

## Managing connections for S3 on Outposts using cross\-account elastic network interfaces<a name="S3OutpostsXENI"></a>

S3 on Outposts endpoints are named resources with proper Amazon Resource Names \(ARNs\)\. When these endpoints are created, AWS Outposts sets up multiple cross\-account elastic network interfaces\. S3 on Outposts cross\-account elastic network interfaces are like other network interfaces with one exception: S3 on Outposts associates the cross\-account elastic network interfaces to instances\. 

The S3 on Outposts Domain Name System \(DNS\) load balances your requests over the cross\-account elastic network interface\. S3 on Outposts creates the cross\-account elastic network interface in your AWS account that is visible from the **Network interfaces** pane of the Amazon EC2 console\. 

For endpoints that use the CoIP pool access type, S3 on Outposts allocates and associates IP addresses with the cross\-account elastic network interface from the configured CoIP pool\.

## Permissions required for S3 on Outposts endpoints<a name="S3OutpostsEndpointPermissions"></a>

For endpoints that are using the customer\-owned IP address pool \(CoIP pool\) access type, you also must have permissions to allocate and associate IP addresses from your CoIP pool\. 

S3 on Outposts requires new permissions in AWS Identity and Access Management \(IAM\) to manage S3 on Outposts endpoint actions\. 


**S3 on Outposts endpoint\-related IAM permissions**  

| Action | IAM permissions | 
| --- | --- | 
| CreateEndpoint |  `s3-outposts:CreateEndpoint` `ec2:CreateNetworkInterface` `ec2:DescribeNetworkInterfaces` `ec2:DescribeVpcs` `ec2:DescribeSecurityGroups` `ec2:DescribeSubnets` `ec2:CreateTags` For endpoints that are using the on\-premises customer\-owned IP address pool \(CoIP pool\) access type, the following additional permissions are required: `s3-outposts:CreateEndpoint` `ec2:DescribeCoipPools` `ec2:GetCoipPoolUsage` `ec2:AllocateAddress` `ec2:AssociateAddress` `ec2:DescribeAddresses` `ec2:DescribeLocalGatewayRouteTableVpcAssociations`  | 
| DeleteEndpoint |  `s3-outposts:DeleteEndpoint` `ec2:DeleteNetworkInterface` `ec2:DescribeNetworkInterfaces` For endpoints that are using the on\-premises customer\-owned IP address pool \(CoIP pool\) access type, the following additional permissions are required: `s3-outposts:DeleteEndpoint` `ec2:DisassociateAddress` `ec2:DescribeAddresses` `ec2:ReleaseAddress`  | 
| ListEndpoints | `s3-outposts:ListEndpoints`  | 

**Note**  
You can use resource tags in an IAM policy to manage permissions\.

## Encryption options with S3 on Outposts<a name="S3OutpostsEncryption"></a>

By default, all data stored in S3 on Outposts is encrypted using server\-side encryption with Amazon S3 managed encryption keys \(SSE\-S3\)\. You can optionally use server\-side encryption with customer\-provided encryption keys \(SSE\-C\) by specifying an encryption key as part of your object API requests\. Server\-side encryption encrypts only the object data, not the object metadata\. 