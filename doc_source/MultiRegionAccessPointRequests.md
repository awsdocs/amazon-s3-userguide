# Making requests through a Multi\-Region Access Point<a name="MultiRegionAccessPointRequests"></a>

Like other resources, Amazon S3 Multi\-Region Access Points have Amazon Resource Names \(ARNs\)\. You can use these ARNs to direct requests to Multi\-Region Access Points by using the AWS Command Line Interface \(AWS CLI\), AWS SDKs, or the Amazon S3 API\. You can also use these ARNs to identify Multi\-Region Access Points in access control policies\. A Multi\-Region Access Point ARN doesn't include or disclose the name of the Multi\-Region Access Point\. For more information about ARNs, see [Amazon Resource Names \(ARNs\)](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html) in the *AWS General Reference*\.

**Note**  
The Multi\-Region Access Point alias and ARN cannot be used interchangeably\.

Multi\-Region Access Point ARNs use the following format:

 `arn:aws:s3::account-id:accesspoint/MultiRegionAccessPoint_alias`

The following are a few examples of Multi\-Region Access Point ARNs: 
+ `arn:aws:s3::123456789012:accesspoint/mfzwi23gnjvgw.mrap` represents the Multi\-Region Access Point with the alias `mfzwi23gnjvgw.mrap`, which is owned by AWS account `123456789012`\. 
+ `arn:aws:s3::123456789012:accesspoint/*` represents all Multi\-Region Access Points under the account `123456789012`\. This ARN matches all Multi\-Region Access Points for account `123456789012`, but doesn't match any Regional Amazon S3 Access Points because the ARN doesn’t include an AWS Region\. In contrast, the ARN `arn:aws:s3:us-west-2:123456789012:accesspoint/*` matches all Regional Amazon S3 Access Points in the Region `us-west-2` for the account `123456789012`, but doesn't match any Multi\-Region Access Points\. 

ARNs for objects that are accessed through a Multi\-Region Access Point use the following format:

 `arn:aws:s3::account_id:accesspoint/MultiRegionAccessPoint_alias//key`

As with Multi\-Region Access Point ARNs, the ARNs for objects that are accessed through Multi\-Region Access Points don't include an AWS Region\. Here are some examples\. 
+ `arn:aws:s3::123456789012:accesspoint/mfzwi23gnjvgw.mrap//-01` represents the `-01`, which is accessed through the Multi\-Region Access Point with the alias `mfzwi23gnjvgw.mrap`, which is owned by account `123456789012`\. 
+ `arn:aws:s3::123456789012:accesspoint/mfzwi23gnjvgw.mrap//*` represents all objects that can be accessed through the Multi\-Region Access Point with the alias `mfzwi23gnjvgw.mrap`, in account `123456789012`\. 
+ `arn:aws:s3::123456789012:accesspoint/mfzwi23gnjvgw.mrap//-01/finance/*` represents all objects that can be accessed under the `-01/finance/` for the Multi\-Region Access Point with the alias `mfzwi23gnjvgw.mrap`, in account `123456789012`\. 

## Multi\-Region Access Point hostnames<a name="MultiRegionAccessPointHostnames"></a>

You can access data in Amazon S3 through a Multi\-Region Access Point by using the hostname of the Multi\-Region Access Point\. Requests can be directed to this hostname from the public internet\. If you have configured one or more internet gateways for the Multi\-Region Access Point, requests can also be directed to this hostname from a virtual private cloud \(VPC\)\. For more information about creating VPC interface endpoints to use with Multi\-Region Access Points, see [Configuring a Multi\-Region Access Point for use with AWS PrivateLink](MultiRegionAccessPointsPrivateLink.md)\. 

To make requests through a Multi\-Region Access Point from a VPC by using a VPC endpoint, you can use AWS PrivateLink\. When you're making requests to a Multi\-Region Access Point by using AWS PrivateLink, you cannot directly use an endpoint\-specific Regional DOMAIN NAME SYSTEM \(DNS\) name that ends with `region.vpce.amazonaws.com`\. This hostname will not have a certificate associated with it, so it cannot be used directly\. You can still use the public DOMAIN NAME SYSTEM \(DNS\) name of the VPC endpoint as a `CNAME` or `ALIAS` target\. Alternatively, you can enable private DOMAIN NAME SYSTEM \(DNS\) on the endpoint and use the standard Multi\-Region Access Point `MultiRegionAccessPoint_alias.accesspoint.s3-global.amazonaws.com` DOMAIN NAME SYSTEM \(DNS\) name, as described in this section\. 

When you make requests to the API for Amazon S3 data operations \(for example, `GetObject`\) through a Multi\-Region Access Point, the hostname for the request is as follows: 

`MultiRegionAccessPoint_alias.accesspoint.s3-global.amazonaws.com` 

For example, to make a `GetObject` request through the Multi\-Region Access Point with the alias `mfzwi23gnjvgw.mrap`, make a request to the hostname `mfzwi23gnjvgw.mrap.accesspoint.s3-global.amazonaws.com`\. The `s3-global` portion of the hostname indicates that this hostname is not for a specific Region\.

Making requests through a Multi\-Region Access Point is similar to making requests through a single\-Region access point\. However, it's important to be aware of the following differences: 
+  Multi\-Region Access Point ARNs don't include an AWS Region\. They follow the format `arn:aws:s3::account-id:accesspoint/MultiRegionAccessPoint_alias`\. 
+  For requests made through API operations \(these requests don't require the use of an ARN\), Multi\-Region Access Points use a different endpoint scheme\. The scheme is `MultiRegionAccessPoint_alias.accesspoint.s3-global.amazonaws.com`—for example, `mfzwi23gnjvgw.mrap.accesspoint.s3-global.amazonaws.com`\. Note the differences compared to a single\-Region access point: 
  + Multi\-Region Access Point hostnames use their alias, not the Multi\-Region Access Point name\. 
  + Multi\-Region Access Point hostnames don't include the owner's AWS account ID\. 
  + Multi\-Region Access Point hostnames don't include an AWS Region\. 
  + Multi\-Region Access Point hostnames include `s3-global.amazonaws.com` instead of `s3.amazonaws.com`\. 
+ Multi\-Region Access Point requests must be signed by using Signature Version 4A \(SigV4A\)\. When you use the AWS SDKs, the SDK automatically converts a SigV4 to SigV4A\. Therefore, make sure that your [AWS SDK supports](https://docs.aws.amazon.com/sdkref/latest/guide/feature-s3-mrap.html) SigV4A as the signing implementation that is used to sign the global AWS Region requests\. For more information about SigV4A, see [Signing AWS API requests](https://docs.aws.amazon.com/general/latest/gr/signing_aws_api_requests.html) in the *AWS General Reference*\. 

## Multi\-Region Access Points and Amazon S3 Transfer Acceleration<a name="MultiRegionAccessPointsAndTransferAcceleration"></a>

Amazon S3 Transfer Acceleration is a feature that enables fast transfer of data to buckets\. Transfer Acceleration is configured on the individual bucket level\. For more information about Transfer Acceleration, see [Configuring fast, secure file transfers using Amazon S3 Transfer Acceleration](transfer-acceleration.md)\. 

Multi\-Region Access Points use a similar accelerated transfer mechanism as Transfer Acceleration for sending large objects over the AWS network\. Because of this, you don't need to use Transfer Acceleration when sending requests through a Multi\-Region Access Point\. This increased transfer performance is automatically incorporated into the Multi\-Region Access Point\. 

**Topics**
+ [Multi\-Region Access Point hostnames](#MultiRegionAccessPointHostnames)
+ [Multi\-Region Access Points and Amazon S3 Transfer Acceleration](#MultiRegionAccessPointsAndTransferAcceleration)
+ [Permissions](MultiRegionAccessPointPermissions.md)
+ [Multi\-Region Access Point restrictions and limitations](MultiRegionAccessPointRestrictions.md)
+ [Multi\-Region Access Point request routing](MultiRegionAccessPointRequestRouting.md)
+ [Amazon S3 Multi\-Region Access Points failover controls](MrapFailover.md)
+ [Configuring replication for use with Multi\-Region Access Points](MultiRegionAccessPointBucketReplication.md)
+ [Using Multi\-Region Access Points with supported API operations](MrapOperations.md)
+ [Monitoring and logging requests made through a Multi\-Region Access Point to underlying resources](MultiRegionAccessPointMonitoring.md)