# AWS PrivateLink for Amazon S3<a name="privatelink-interface-endpoints"></a>

With AWS PrivateLink for Amazon S3, you can provision *interface VPC endpoints* \(interface endpoints\) in your virtual private cloud \(VPC\) instead of connecting over the internet\. These endpoints are directly accessible from applications that are on premises over VPN and AWS Direct Connect, or in a different AWS Region over VPC peering\.

Interface endpoints are represented by one or more elastic network interfaces \(ENIs\) that are assigned private IP addresses from subnets in your VPC\. Requests that are made to interface endpoints for Amazon S3 are automatically routed to Amazon S3 on the Amazon network\. You can also access interface endpoints in your VPC from on\-premises applications through AWS Direct Connect or AWS Virtual Private Network \(AWS VPN\)\. For more information about how to connect your VPC with your on\-premises network, see the [AWS Direct Connect User Guide](https://docs.aws.amazon.com/directconnect/latest/UserGuide/Welcome.html) and the [AWS Site\-to\-Site VPN User Guide](https://docs.aws.amazon.com/vpn/latest/s2svpn/VPC_VPN.html)\. 

For general information about interface endpoints, see [Interface VPC endpoints \(AWS PrivateLink\)](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-interface.html) in the *AWS PrivateLink Guide*\.

**Topics**
+ [Types of VPC endpoints for Amazon S3](#types-of-vpc-endpoints-for-s3)
+ [Restrictions and limitations of AWS PrivateLink for Amazon S3](#privatelink-limitations)
+ [Accessing Amazon S3 interface endpoints](#accessing-s3-interface-endpoints)
+ [Accessing buckets and S3 access points from S3 interface endpoints](#accessing-bucket-and-aps-from-interface-endpoints)
+ [Updating an on\-premises DNS configuration](#updating-on-premises-dns-config)
+ [Creating a VPC endpoint policy for Amazon S3](#creating-vpc-endpoint-policy)

## Types of VPC endpoints for Amazon S3<a name="types-of-vpc-endpoints-for-s3"></a>

You can use two types of VPC endpoints to access Amazon S3: *gateway endpoints* and *interface endpoints*\. A *gateway endpoint* is a gateway that you specify in your route table to access Amazon S3 from your VPC over the AWS network\. *Interface endpoints* extend the functionality of gateway endpoints by using private IP addresses to route requests to Amazon S3 from within your VPC, on premises, or from a different AWS Region\. Interface endpoints are compatible with gateway endpoints\. If you have an existing gateway endpoint in the VPC, you can use both types of endpoints in the same VPC\.


|  Gateway endpoints for Amazon S3  |  Interface endpoints for Amazon S3  | 
| --- | --- | 
|   In both cases, your network traffic remains on the AWS network\.  | 
|  Use Amazon S3 public IP addresses  |  Use private IP addresses from your VPC to access Amazon S3  | 
|  Does not allow access from on premises  |  Allows access from on premises  | 
|  Does not allow access from another AWS Region  |  Allows access from another AWS Region  | 
|  Not billed  |  Billed  | 

For more information about gateway endpoints, see [Gateway VPC endpoints](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-gateway.html) in the *AWS PrivateLink Guide*\.

## Restrictions and limitations of AWS PrivateLink for Amazon S3<a name="privatelink-limitations"></a>

VPC limitations apply to AWS PrivateLink for Amazon S3\. For more information, see [Interface endpoint properties and limitations](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-interface.html#vpce-interface-limitations) and [AWS PrivateLink quotas](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-limits-endpoints.html) in the *AWS PrivateLink Guide*\. In addition, the following restrictions apply\.

AWS PrivateLink for Amazon S3 does not support the following:
+ [Federal Information Processing Standard \(FIPS\) endpoints](http://aws.amazon.com/compliance/fips/)
+ [Website endpoints](WebsiteEndpoints.md)
+ [Legacy global endpoints](VirtualHosting.md#deprecated-global-endpoint)

## Accessing Amazon S3 interface endpoints<a name="accessing-s3-interface-endpoints"></a>

**Important**  
To access Amazon S3 using AWS PrivateLink, you *must* update your applications to use endpoint\-specific DNS names\.

When you create an interface endpoint, Amazon S3 generates two types of endpoint\-specific, S3 DNS names: *Regional* and *zonal*\. 
+ *Regional* DNS names include a unique VPC endpoint ID, a service identifier, the AWS Region, and `vpce.amazonaws.com` in its name\. For example, for VPC endpoint ID `vpce-1a2b3c4d`, the DNS name generated might be similar to `vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com`\.
+ *Zonal* DNS names include the Availability Zone—for example, `vpce-1a2b3c4d-5e6f-us-east-1a.s3.us-east-1.vpce.amazonaws.com`\. You might use this option if your architecture isolates Availability Zones\. For example, you could use it for fault containment or to reduce Regional data transfer costs\.

Endpoint\-specific S3 DNS names can be resolved from the S3 public DNS domain\.

**Note**  
Amazon S3 interface endpoints do *not* support the private DNS feature of interface endpoints\. For more information about [Private DNS for interface endpoints](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-interface.html#vpce-private-dns), see the *AWS PrivateLink Guide*\.

## Accessing buckets and S3 access points from S3 interface endpoints<a name="accessing-bucket-and-aps-from-interface-endpoints"></a>

You can use the AWS CLI or AWS SDK to access buckets, S3 access points, and S3\-control APIs through S3 interface endpoints\. 

The following image shows the VPC console **Details** tab, where you can find the DNS name of a VPC endpoint\. In this example, the *VPC endpoint ID \(vpce\-id\)* is `vpce-0e25b8cdd720f900e` and the *DNS name* is `vpce-0e25b8cdd720f900e-argc85vg.s3.us-east-1.vpce.amazonaws.com`\.

![\[Screenshot of Details tab from the VPC console.\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/vpc-console-details-tab.png)

For more about how to view your endpoint\-specific DNS names, see [Viewing endpoint service private DNS name configuration](https://docs.aws.amazon.com/vpc/latest/privatelink/view-vpc-endpoint-service-dns-name.html) in the *VPC User Guide*\.

### AWS CLI examples<a name="privatelink-aws-cli-examples"></a>

Use the `--region` and `--endpoint-url` parameters to access S3 buckets, S3 access points, or S3 control APIs through S3 interface endpoints\. 

**Example: Use the endpoint URL to list objects in your bucket**  
In the following example, replace the region `us-east-1`, VPC endpoint ID `vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com`, and bucket name `my-bucket` with appropriate information\.

```
aws s3 --region us-east-1 --endpoint-url https://bucket.vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com ls s3://my-bucket/
```

**Example: Use the endpoint URL to list objects from an access point**  
In the following example, replace the ARN `us-east-1:123456789012:accesspoint/test`, region `us-east-1`, and VPC endpoint ID `vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com` with appropriate information\.

```
aws s3api list-objects-v2 --bucket arn:aws:s3:us-east-1:123456789012:accesspoint/test --region us-east-1 --endpoint-url https://accesspoint.vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com
```

**Example: Use the endpoint URL to list jobs with S3 control**  
In the following example, replace the region `us-east-1`, VPC endpoint ID `vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com`, and account ID `12345678` with appropriate information\.

```
aws s3control --region us-east-1 --endpoint-url https://control.vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com list-jobs --account-id 12345678
```

### AWS SDK examples<a name="privatelink-aws-sdk-examples"></a>

Update your SDKs to the latest version, and configure your clients to use an endpoint URL for accessing a bucket, access point, or S3 control API through S3 interface endpoints\. 

------
#### [ SDK for Python \(Boto3\) ]

**Example: Use an endpoint URL to access an S3 bucket**  
In the following example, replace the region `us-east-1` and VPC endpoint ID `vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com` with appropriate information\. 

```
s3_client = session.client(
service_name='s3',
region_name='us-east-1',
endpoint_url='https://bucket.vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com'
)
```

**Example: Use an endpoint URL to access an S3 access point**  
In the following example, replace the region `us-east-1` and VPC endpoint ID `vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com` with appropriate information\. 

```
ap_client = session.client(
service_name='s3',
region_name='us-east-1',
endpoint_url='https://accesspoint.vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com'
)
```

**Example: Use an endpoint URL to access the S3 control API**  
In the following example, replace the region `us-east-1` and VPC endpoint ID `vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com` with appropriate information\. 

```
control_client = session.client(
service_name='s3control',
region_name='us-east-1',
endpoint_url='https://control.vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com'
)
```

------
#### [ SDK for Java 1\.x ]

**Example: Use an endpoint URL to access an S3 bucket**  
In the following example, replace the VPC endpoint ID `vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com` with appropriate information\. 

```
// bucket client
final AmazonS3 s3 = AmazonS3ClientBuilder.standard().withEndpointConfiguration(
        new AwsClientBuilder.EndpointConfiguration(
                "https://bucket.vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com",
                Regions.DEFAULT_REGION.getName()
        )
).build();
List<Bucket> buckets = s3.listBuckets();
```

**Example: Use an endpoint URL to access an S3 access point**  
In the following example, replace the VPC endpoint ID `vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com` and ARN `us-east-1:123456789012:accesspoint/prod` with appropriate information\. 

```
// accesspoint client
final AmazonS3 s3accesspoint = AmazonS3ClientBuilder.standard().withEndpointConfiguration(
        new AwsClientBuilder.EndpointConfiguration(
                "https://accesspoint.vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com",
                Regions.DEFAULT_REGION.getName()
        )
).build();
ObjectListing objects = s3accesspoint.listObjects("arn:aws:s3:us-east-1:123456789012:accesspoint/prod");
```

**Example: Use an endpoint URL to access the S3 control API**  
In the following example, replace the VPC endpoint ID `vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com` with appropriate information\. 

```
// control client
final AWSS3Control s3control = AWSS3ControlClient.builder().withEndpointConfiguration(
        new AwsClientBuilder.EndpointConfiguration(
                "https://control.vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com",
                Regions.DEFAULT_REGION.getName()
        )
).build();
final ListJobsResult jobs = s3control.listJobs(new ListJobsRequest());
```

------
#### [ SDK for Java 2\.x ]

**Example: Use an endpoint URL to access an S3 bucket**  
In the following example, replace the VPC endpoint ID `vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com` and the Region `Region.US_EAST_1` with appropriate information\.

```
// bucket client
Region region = Region.US_EAST_1;
s3Client = S3Client.builder().region(region)
                   .endpointOverride(URI.create("https://bucket.vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com"))
                   .build()
```

**Example: Use an endpoint URL to access an S3 access point**  
In the following example, replace the VPC endpoint ID `vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com` and the Region `Region.US_EAST_1` with appropriate information\.

```
// accesspoint client
Region region = Region.US_EAST_1;
s3Client = S3Client.builder().region(region)
                   .endpointOverride(URI.create("https://accesspoint.vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com"))
                   .build()
```

**Example: Use an endpoint URL to access the S3 control API**  
In the following example, replace the VPC endpoint ID `vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com` and the Region `Region.US_EAST_1` with appropriate information\.

```
// control client
Region region = Region.US_EAST_1;
s3ControlClient = S3ControlClient.builder().region(region)
                                 .endpointOverride(URI.create("https://control.vpce-1a2b3c4d-5e6f.s3.us-east-1.vpce.amazonaws.com"))
                                 .build()
```

------

## Updating an on\-premises DNS configuration<a name="updating-on-premises-dns-config"></a>

When using endpoint\-specific DNS names to access the interface endpoints for Amazon S3, you don’t have to update your on\-premises DNS resolver\. You can resolve the endpoint\-specific DNS name with the private IP address of the interface endpoint from the public Amazon S3 DNS domain\. 

### Using interface endpoints to access Amazon S3 without a gateway endpoint or an internet gateway in the VPC<a name="using-interface-endpoints"></a>

Interface endpoints in your VPC can route both in\-VPC applications and on\-premises applications to Amazon S3 over the Amazon network, as illustrated in the following diagram\.

![\[Data flow diagram shows access from on-premises and in-VPC apps to S3 using an interface endpoint and AWS PrivateLink.\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/interface-endpoints.png)

The diagram illustrates the following: 
+ Your on\-premises network uses AWS Direct Connect or AWS VPN to connect to VPC A\.
+ Your applications on\-premises and in VPC A use endpoint\-specific DNS names to access Amazon S3 through the S3 interface endpoint\.
+ On\-premises applications send data to the interface endpoint in the VPC through AWS Direct Connect \(or AWS VPN\)\. AWS PrivateLink moves the data from the interface endpoint to Amazon S3 over the AWS network\.
+ In\-VPC applications also send traffic to the interface endpoint\. AWS PrivateLink moves the data from the interface endpoint to Amazon S3 over the AWS network\.

### Using gateway endpoints and interface endpoints together in the same VPC to access Amazon S3<a name="using-gateway-and-interface-endpoints"></a>

You can create interface endpoints and retain the existing gateway endpoint in the same VPC, as the following diagram shows\. By doing this, you allow in\-VPC applications to continue accessing Amazon S3 through the gateway endpoint, which is not billed\. Then, only your on\-premises applications would use interface endpoints to access Amazon S3\. To access S3 this way, you must update your on\-premises applications to use endpoint\-specific DNS names for Amazon S3\.

![\[Data flow diagram shows access to S3 using gateway endpoints and interface endpoints together.\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/interface-and-gateway-endpoints.png)

The diagram illustrates the following: 
+ On\-premises applications use endpoint\-specific DNS names to send data to the interface endpoint within the VPC through AWS Direct Connect \(or AWS VPN\)\. AWS PrivateLink moves the data from the interface endpoint to Amazon S3 over the AWS network\.
+ Using default Regional Amazon S3 names, in\-VPC applications send data to the gateway endpoint that connects to Amazon S3 over the AWS network\.

For more information about gateway endpoints, see [Gateway VPC endpoints](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-gateway.html) in the *VPC User Guide*\. 

## Creating a VPC endpoint policy for Amazon S3<a name="creating-vpc-endpoint-policy"></a>

You can attach an endpoint policy to your VPC endpoint that controls access to Amazon S3\. The policy specifies the following information: 
+ The AWS Identity and Access Management \(IAM\) principal that can perform actions 
+ The actions that can be performed 
+ The resources on which actions can be performed 

You can also use Amazon S3 bucket policies to restrict access to specific buckets from a specific VPC endpoint using the `aws:sourceVpce` condition in your bucket policy\. The following examples show policies that restrict access to a bucket or to an endpoint\.

**Topics**
+ [Example: Restricting access to a specific bucket from a VPC endpoint](#privatelink-example-restrict-access-to-bucket)
+ [Example: Restricting access to buckets in a specific account from a VPC endpoint](#privatelink-example-access-bucket-in-specific-account-only)
+ [Example: Restricting access to a specific VPC endpoint in the S3 bucket policy](#privatelink-example-restrict-access-to-vpc-endpoint)

**Important**  
When applying the Amazon S3 bucket policies for VPC endpoints described in this section, you might block your access to the bucket without intending to do so\. Bucket permissions that are intended to specifically limit bucket access to connections originating from your VPC endpoint can block all connections to the bucket\. For information about how to fix this issue, see [My bucket policy has the wrong VPC or VPC endpoint ID\. How can I fix the policy so that I can access the bucket?](https://aws.amazon.com/premiumsupport/knowledge-center/s3-regain-access/) in the *AWS Support Knowledge Center*\.
Before using the following example policy, replace the VPC endpoint ID with an appropriate value for your use case\. Otherwise, you won't be able to access your bucket\. 
This policy disables *console* access to the specified bucket, because console requests don't originate from the specified VPC endpoint\. 

### Example: Restricting access to a specific bucket from a VPC endpoint<a name="privatelink-example-restrict-access-to-bucket"></a>

You can create an endpoint policy that restricts access to specific Amazon S3 buckets only\. This is useful if you have other AWS services in your VPC that use buckets\. The following bucket policy restricts access to `DOC-EXAMPLE-BUCKET1` only\. Replace `DOC-EXAMPLE-BUCKET1` with the name of your bucket\.

```
{
  "Version": "2012-10-17",
  "Id": "Policy1415115909151",
  "Statement": [
    { "Sid": "Access-to-specific-bucket-only",
      "Principal": "*",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Effect": "Allow",
      "Resource": ["arn:aws:s3:::DOC-EXAMPLE-BUCKET1",
                   "arn:aws:s3:::DOC-EXAMPLE-BUCKET1/*"]
    }
  ]
}
```

### Example: Restricting access to buckets in a specific account from a VPC endpoint<a name="privatelink-example-access-bucket-in-specific-account-only"></a>

You can create a policy that restricts access only to the S3 buckets in a specific AWS account\. Use this to prevent clients within your VPC from accessing buckets that you do not own\. The following example creates a policy that restricts access to resources owned by a single AWS account ID, *111122223333*\.

```
{
  "Statement": [
    {
      "Sid": "Access-to-bucket-in-specific-account-only",
      "Principal": "*",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Effect": "Deny",
      "Resource": "arn:aws:s3:::*",
      "Condition": {
        "StringNotEquals": {
          "s3:ResourceAccount": "111122223333"
        }
      }
    }
  ]
}
```

### Example: Restricting access to a specific VPC endpoint in the S3 bucket policy<a name="privatelink-example-restrict-access-to-vpc-endpoint"></a>

**Example: Restricting access to a specific VPC endpoint in the S3 bucket policy**

The following Amazon S3 bucket policy allows access to a specific bucket, `DOC-EXAMPLE-BUCKET2`, from endpoint `vpce-1a2b3c4d` only\. The policy denies all access to the bucket if the specified endpoint is not being used\. The `aws:sourceVpce` condition is used to specify the endpoint and does not require an Amazon Resource Name \(ARN\) for the VPC endpoint resource, only the endpoint ID\. Replace `DOC-EXAMPLE-BUCKET2` and `vpce-1a2b3c4d` with a real bucket name and endpoint\. 

```
{
  "Version": "2012-10-17",
  "Id": "Policy1415115909152",
  "Statement": [
    { "Sid": "Access-to-specific-VPCE-only",
      "Principal": "*",
      "Action": "s3:*",
      "Effect": "Deny",
      "Resource": ["arn:aws:s3:::DOC-EXAMPLE-BUCKET2",
                   "arn:aws:s3:::DOC-EXAMPLE-BUCKET2/*"],
      "Condition": {"StringNotEquals": {"aws:sourceVpce": "vpce-1a2b3c4d"}}
    }
  ]
}
```

For more policy examples, see [Endpoints for Amazon S3](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints-s3.html#vpc-endpoints-policies-s3) in the *VPC User Guide*\.

For more information about VPC connectivity, see [Network\-to\-VPC connectivity options](https://docs.aws.amazon.com/whitepapers/latest/aws-vpc-connectivity-options/network-to-amazon-vpc-connectivity-options.html) in the AWS whitepaper [Amazon Virtual Private Cloud Connectivity Options](https://docs.aws.amazon.com/whitepapers/latest/aws-vpc-connectivity-options/welcome.html)\.