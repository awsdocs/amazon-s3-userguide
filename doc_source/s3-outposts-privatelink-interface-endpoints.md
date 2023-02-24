# AWS PrivateLink for S3 on Outposts<a name="s3-outposts-privatelink-interface-endpoints"></a>

With AWS PrivateLink for Amazon S3 on Outposts, you can provision *interface VPC endpoints* in your virtual private cloud \(VPC\) to manage your S3 on Outposts deployment through the S3 on Outposts [ bucket management](S3OutpostsAPI.md#S3OutpostsAPIsBucket) and [endpoint management](S3OutpostsAPI.md#S3OutpostsAPIs) APIs\. Interface VPC endpoints are directly accessible from applications that are deployed in your VPC or on premises over your virtual private network \(VPN\) or AWS Direct Connect\. You can access the bucket management and endpoint management APIs through AWS PrivateLink\. AWS PrivateLink doesn't support [data transfer](S3OutpostsAPI.md#S3OutpostsAPIsObject) API operations, such as GET, PUT, and similar APIs\. These operations are transferred privately through the S3 on Outposts endpoint and access point configuration\. For more information, see [Networking for S3 on Outposts](S3OutpostsNetworking.md)\.

Interface endpoints are represented by one or more elastic network interfaces \(ENIs\) that are assigned private IP addresses from subnets in your VPC\. Requests that are made to interface endpoints for S3 on Outposts are automatically routed to S3 on Outposts bucket management and endpoint management APIs on the Amazon network\. You can also access interface endpoints in your VPC from on\-premises applications through AWS Direct Connect or AWS Virtual Private Network \(AWS VPN\)\. For more information about how to connect your VPC with your on\-premises network, see the [https://docs.aws.amazon.com/directconnect/latest/UserGuide/Welcome.html](https://docs.aws.amazon.com/directconnect/latest/UserGuide/Welcome.html) and the [https://docs.aws.amazon.com/vpn/latest/s2svpn/VPC_VPN.html](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPC_VPN.html)\. 

Interface endpoints route requests for S3 on Outposts bucket management and endpoint management APIs over the Amazon network and through AWS PrivateLink, as illustrated in the following diagram\.

![\[Data flow diagram shows how interface endpoints route requests for S3 on Outposts bucket management and endpoint management APIs over the Amazon network and using AWS PrivateLink.\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/s3-outposts-interface-endpoints.png)

For general information about interface endpoints, see [Interface VPC endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-interface.html) in the *AWS PrivateLink Guide*\.

**Topics**
+ [Restrictions and limitations](#s3-outposts-privatelink-restrictions)
+ [Accessing S3 on Outposts interface endpoints](#s3-outposts-accessing-s3-interface-endpoints)
+ [Updating an on\-premises DNS configuration](#s3-outposts-updating-on-premises-dns-config)
+ [Creating a VPC endpoint for S3 on Outposts](#s3-outposts-creating-vpc)
+ [Creating bucket policies and VPC endpoint policies for S3 on Outposts](#s3-outposts-creating-vpc-endpoint-policy)

## Restrictions and limitations<a name="s3-outposts-privatelink-restrictions"></a>

When you access S3 on Outposts bucket management and endpoint management APIs through AWS PrivateLink, VPC limitations apply\. For more information, see [Interface endpoint properties and limitations](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-interface.html#vpce-interface-limitations) and [AWS PrivateLink quotas](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-limits-endpoints.html) in the *AWS PrivateLink Guide*\. 

In addition, AWS PrivateLink doesn't support the following:
+ [Federal Information Processing Standard \(FIPS\) endpoints](http://aws.amazon.com/compliance/fips/)
+ [S3 on Outposts data transfer APIs](S3OutpostsAPI.md#S3OutpostsAPIsObject), for example, GET, PUT, and similar object API operations\.

## Accessing S3 on Outposts interface endpoints<a name="s3-outposts-accessing-s3-interface-endpoints"></a>

To access S3 on Outposts bucket management and endpoint management APIs using AWS PrivateLink, you *must* update your applications to use endpoint\-specific DNS names\. When you create an interface endpoint, AWS PrivateLink generates two types of endpoint\-specific S3 on Outposts names: *Regional* and *zonal*\.
+ **Regional DNS names** – include a unique VPC endpoint ID, a service identifier, the AWS Region, and `vpce.amazonaws.com`, for example, `vpce-1a2b3c4d-5e6f.s3-outposts.us-east-1.vpce.amazonaws.com`\.
+ **Zonal DNS names** – include a unique VPC endpoint ID, the Availability Zone, a service identifier, the AWS Region, and `vpce.amazonaws.com`, for example, `vpce-1a2b3c4d-5e6f-us-east-1a.s3-outposts.us-east-1.vpce.amazonaws.com`\. You might use this option if your architecture isolates Availability Zones\. For example, you could use zonal DNS names for fault containment or to reduce Regional data transfer costs\.

Endpoint\-specific S3 on Outposts DNS names can be resolved from the S3 on Outposts public DNS domain\. S3 on Outposts interface endpoints also support the private DNS feature of interface endpoints\. For more information about [Private DNS for interface endpoints](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-interface.html#vpce-private-dns), see the *AWS PrivateLink Guide*\.

**Note**  
If you enable Private DNS on your VPC endpoints, the `--endpoint-url` parameter isn’t required when using the AWS CLI and AWS SDKs\.

### AWS CLI examples<a name="s3-outposts-privatelink-aws-cli-examples"></a>

Use the `--region` and `--endpoint-url` parameters to access bucket management and endpoint management APIs through S3 on Outposts interface endpoints\. 

**Example : Use the endpoint URL to list buckets with the S3 control API**  
In the following example, replace the Region `us-east-1`, VPC endpoint URL `vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com`, and account ID `111122223333` with appropriate information\.  

```
aws s3control list-regional-buckets --region us-east-1 --endpoint-url https://vpce-1a2b3c4d-5e6f.s3-outposts.us-east-1.vpce.amazonaws.com --account-id 111122223333
```

### AWS SDK examples<a name="s3-outposts-privatelink-aws-sdk-examples"></a>

Update your SDKs to the latest version, and configure your clients to use an endpoint URL for accessing the S3 control API for S3 on Outposts interface endpoints\.

------
#### [ SDK for Python \(Boto3\) ]

**Example : Use an endpoint URL to access the S3 control API**  
In the following example, replace the Region `us-east-1` and VPC endpoint URL `vpce-1a2b3c4d-5e6f.s3-outposts.us-east-1.vpce.amazonaws.com` with appropriate information\.   

```
control_client = session.client(
service_name='s3control',
region_name='us-east-1',
endpoint_url='https://vpce-1a2b3c4d-5e6f.s3-outposts.us-east-1.vpce.amazonaws.com'
)
```
For more information, see [AWS PrivateLink for Amazon S3](https://boto3.amazonaws.com/v1/documentation/api/latest/guide/s3-example-privatelink.html) in the *Boto3 developer guide*\.

------
#### [ SDK for Java 2\.x ]

**Example : Use an endpoint URL to access the S3 control API**  
In the following example, replace the VPC endpoint URL `vpce-1a2b3c4d-5e6f.s3-outposts.us-east-1.vpce.amazonaws.com` and the Region `Region.US_EAST_1` with appropriate information\.  

```
// control client
Region region = Region.US_EAST_1;
s3ControlClient = S3ControlClient.builder().region(region)
                                 .endpointOverride(URI.create("https://vpce-1a2b3c4d-5e6f.s3-outposts.us-east-1.vpce.amazonaws.com"))
                                 .build()
```
For more information, see [https://sdk.amazonaws.com/java/api/latest/software/amazon/awssdk/services/s3control/S3ControlClient.html](https://sdk.amazonaws.com/java/api/latest/software/amazon/awssdk/services/s3control/S3ControlClient.html) in the *AWS SDK for Java API Reference*\.

------

## Updating an on\-premises DNS configuration<a name="s3-outposts-updating-on-premises-dns-config"></a>

When using endpoint\-specific DNS names to access the interface endpoints for S3 on Outposts bucket management and endpoint management APIs, you don’t have to update your on\-premises DNS resolver\. You can resolve the endpoint\-specific DNS name with the private IP address of the interface endpoint from the public S3 on Outposts DNS domain\. 

## Creating a VPC endpoint for S3 on Outposts<a name="s3-outposts-creating-vpc"></a>

To create a VPC interface endpoint for S3 on Outposts, see [ Create a VPC endpoint](https://docs.aws.amazon.com/vpc/latest/privatelink/create-interface-endpoint.html#create-interface-endpoint-aws) in the *AWS PrivateLink Guide*\.

## Creating bucket policies and VPC endpoint policies for S3 on Outposts<a name="s3-outposts-creating-vpc-endpoint-policy"></a>

You can attach an endpoint policy to your VPC endpoint that controls access to S3 on Outposts\. You can also use the `aws:sourceVpce` condition in S3 on Outposts bucket policies to restrict access to specific buckets from a specific VPC endpoint\. With VPC endpoint policies, you can control access to S3 on Outposts bucket management APIs and endpoint management APIs\. With bucket policies, you can control access to the S3 on Outposts bucket management APIs\. However, you can't manage access to object actions for S3 on Outposts using `aws:sourceVpce`\.

Access policies for S3 on Outposts specify the following information: 
+ The AWS Identity and Access Management \(IAM\) principal for which actions are allowed or denied\.
+ The S3 control actions that are allowed or denied\.
+ The S3 on Outposts resources on which actions are allowed or denied\.

The following examples show policies that restrict access to a bucket or to an endpoint\. For more information about VPC connectivity, see [Network\-to\-VPC connectivity options](https://docs.aws.amazon.com/whitepapers/latest/aws-vpc-connectivity-options/network-to-amazon-vpc-connectivity-options.html) in the AWS whitepaper [Amazon Virtual Private Cloud Connectivity Options](https://docs.aws.amazon.com/whitepapers/latest/aws-vpc-connectivity-options/welcome.html)\.

**Important**  
When you apply the example policies for VPC endpoints described in this section, you might block your access to the bucket without intending to do so\. Bucket permissions that limit bucket access to connections originating from your VPC endpoint can block all connections to the bucket\. For information about how to fix this issue, see [My bucket policy has the wrong VPC or VPC endpoint ID\. How can I fix the policy so that I can access the bucket?](https://aws.amazon.com/premiumsupport/knowledge-center/s3-regain-access/) in the *AWS Support Knowledge Center*\.
Before using the following example bucket policies, replace the VPC endpoint ID with an appropriate value for your use case\. Otherwise, you won't be able to access your bucket\. 
If your policy only allows access to an S3 on Outposts bucket from a specific VPC endpoint, it disables console access for that bucket because console requests don't originate from the specified VPC endpoint\.

**Topics**
+ [Example: Restricting access to a specific bucket from a VPC endpoint](#privatelink-example-restrict-access-to-bucket)
+ [Example: Denying access from a specific VPC endpoint in an S3 on Outposts bucket policy](#s3-outposts-privatelink-example-deny-access-from-vpc-endpoint)

### Example: Restricting access to a specific bucket from a VPC endpoint<a name="privatelink-example-restrict-access-to-bucket"></a>

You can create an endpoint policy that restricts access to specific S3 on Outposts buckets only\. The following policy restricts access for the GetBucketPolicy action only to the `example-outpost-bucket`\. To use this policy, replace the example values with your own\. 

```
{
  "Version": "2012-10-17",
  "Id": "Policy1415115909151",
  "Statement": [
    { "Sid": "Access-to-specific-bucket-only",
      "Principal": {"AWS":"111122223333"},
      "Action": "s3-outposts:GetBucketPolicy",
      "Effect": "Allow",
      "Resource": "arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/bucket/example-outpost-bucket"
    }
  ]
}
```

### Example: Denying access from a specific VPC endpoint in an S3 on Outposts bucket policy<a name="s3-outposts-privatelink-example-deny-access-from-vpc-endpoint"></a>

The following S3 on Outposts bucket policy denies access to GetBucketPolicy on the `example-outpost-bucket` bucket through the `vpce-1a2b3c4d` VPC endpoint\.

The `aws:sourceVpce` condition specifies the endpoint and does not require an Amazon Resource Name \(ARN\) for the VPC endpoint resource, only the endpoint ID\. To use this policy, replace the example values with your own\.

```
{
    "Version": "2012-10-17",
    "Id": "Policy1415115909152",
    "Statement": [
        {
            "Sid": "Deny-access-to-specific-VPCE",
            "Principal": {"AWS":"111122223333"},
            "Action": "s3-outposts:GetBucketPolicy",
            "Effect": "Deny",
            "Resource": "arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/bucket/example-outpost-bucket",
            "Condition": {
                "StringEquals": {"aws:sourceVpce": "vpce-1a2b3c4d"}
            }
        }
    ]
}
```