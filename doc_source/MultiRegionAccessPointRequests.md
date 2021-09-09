# Making requests using a Multi\-Region Access Point<a name="MultiRegionAccessPointRequests"></a>

Multi\-Region Access Points in Amazon S3 have Amazon Resource Names \(ARNs\), which you can use to direct requests to them using the AWS SDKs and to identify a Multi\-Region Access Point in access control policies\. A Multi\-Region Access Point ARN doesn’t include or disclose its name\. For more information about ARNs, see [Amazon Resource Names \(ARNs\)](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html) in the *AWS General Reference*\. 

Multi\-Region Access Point ARNs use the format `arn:aws:s3::<account-id>:accesspoint/<MRAP_alias>`\. The following are a few examples\. 
+  `arn:aws:s3::123456789012:accesspoint/mfzwi23gnjvgw.mrap` represents the Multi\-Region Access Point with the alias `mfzwi23gnjvgw.mrap`, owned by AWS account `123456789012`\. 
+  `arn:aws:s3::123456789012:accesspoint/*` represents all Multi\-Region Access Points under account `123456789012`\. This ARN matches all Multi\-Region Access Points for account `123456789012`, but doesn’t match any Regional access points because the ARN doesn’t include an AWS Region\. In contrast, the ARN `arn:aws:s3:us-west-2:123456789012:accesspoint/*` matches all Regional access points in Region `us-west-2` for account `123456789012`, but doesn’t match any Multi\-Region Access Points\. 

 ARNs for objects that are accessed through a Multi\-Region Access Point use the format `arn:aws:s3::<account_id>:accesspoint/<MRAP_alias>/object/<key>`\. As with Multi\-Region Access Point ARNs, the ARNs for objects that are accessed through Multi\-Region Access Points don’t include an AWS Region\. Here are some examples\. 
+  `arn:aws:s3::123456789012:accesspoint/mfzwi23gnjvgw.mrap/object/unit-01` represents the object `unit-01`, accessed through the Multi\-Region Access Point with the alias `mfzwi23gnjvgw.mrap`, owned by account `123456789012`\. 
+  `arn:aws:s3::123456789012:accesspoint/mfzwi23gnjvgw.mrap/object/*` represents all objects that can be accessed through the Multi\-Region Access Point with alias `mfzwi23gnjvgw.mrap`, in account `123456789012`\. 
+  `arn:aws:s3::123456789012:accesspoint/mfzwi23gnjvgw.mrap/object/unit-01/finance/*` represents all objects that can be accessed under prefix `unit-01/finance/` for the Multi\-Region Access Point with alias `mfzwi23gnjvgw.mrap`, in account `123456789012`\. 

## Multi\-Region Access Point hostnames<a name="MultiRegionAccessPointHostnames"></a>

 You can access data in Amazon S3 through a Multi\-Region Access Point using the hostname of the Multi\-Region Access Point\. Requests can be directed to this hostname from the public internet or from a virtual private cloud \(VPC\) if you have configured one or more internet gateways for the Multi\-Region Access Point\. For more information about creating VPC interface endpoints to use with Multi\-Region Access Points, see [Configuring a Multi\-Region Access Point for use with AWS PrivateLink](MultiRegionAccessPointsPrivateLink.md)\. 

You can also make requests through a Multi\-Region Access Point from a VPC using AWS PrivateLink if you have configured a VPC endpoint\. Be aware that with requests to a Multi\-Region Access Point using AWS PrivateLink, you cannot directly use an endpoint\-specific Regional DNS ending with `<Region>.vpce.amazonaws.com`\. This hostname will not have a cert associated with it so it cannot be used directly\. You can still use the public DNS name of the VPC endpoint as a `CNAME` or `ALIAS` target\. Alternatively, you can enable private DNS on the endpoint and use the standard Multi\-Region Access Point `<MRAP_alias>.accesspoint.s3-global.amazonaws.com` DNS names as described below\. 

 When you use the REST APIs for Amazon S3 data operations \(for example, `GetObject`\) through a Multi\-Region Access Point, the hostname for the request is `<MRAP_alias>.accesspoint.s3-global.amazonaws.com`\. For example, to make a `GetObject` request through the Multi\-Region Access Point with alias `mfzwi23gnjvgw.mrap`, make a request to the hostname `mfzwi23gnjvgw.mrap.accesspoint.s3-global.amazonaws.com`\. Note the `s3-global` portion of the hostname that indicates this hostname is not for a specific Region\.

 Making requests through a Multi\-Region Access Point is similar to making requests through a single\-Region access point\. It is important to be aware of the following differences: 
+  Multi\-Region Access Point ARNs don’t include an AWS Region\. They follow the format `arn:aws:s3::<account-id>:accesspoint/<MRAP_alias>`\. 
+  For requests made through the REST APIs \(this does not require the use of an ARN\), Multi\-Region Access Points use a different endpoint scheme\. The scheme is `<MRAP_alias>.accesspoint.s3-global.amazonaws.com`—for example, `mfzwi23gnjvgw.mrap.accesspoint.s3-global.amazonaws.com`\. Note the differences compared to a single\-Region access point: 
  +  Multi\-Region Access Point hostnames use their alias, not the Multi\-Region Access Point name\. 
  +  Multi\-Region Access Point hostnames don’t include the owner’s AWS account ID\. 
  +  Multi\-Region Access Point hostnames don’t include an AWS Region\. 
  +  Multi\-Region Access Point hostnames include `s3-global.amazonaws.com` instead of `s3.amazonaws.com`\. 
+  Requests must be signed using Signature Version 4A \(SigV4A\)\. When you use the AWS SDK, the SDK automatically converts a SigV4 signature to SigV4A\. For more information about SigV4A, see [Signing AWS API requests](https://docs.aws.amazon.com/general/latest/gr/signing_aws_api_requests.html) in the *AWS General Reference*\. 

## Multi\-Region Access Points and Amazon S3 Transfer Acceleration<a name="MultiRegionAccessPointsAndTransferAcceleration"></a>

 Amazon S3 Transfer Acceleration is a feature that enables fast transfer of data to buckets\. It is configured on the individual bucket level and you can use it to transfer objects faster to buckets\. To read more about Transfer Acceleration, see [Configuring fast, secure file transfers using Amazon S3 Transfer Acceleration](transfer-acceleration.md)\. 

 When dealing with Multi\-Region Access Points, it is important to know that Multi\-Region Access Points use a similar accelerated transfer mechanism as Transfer Acceleration for sending large objects over the AWS network\. Because of this, you don't need to perform any special configuration or handling to gain the benefits of the faster transfer rates when sending requests through a Multi\-Region Access Point\. This increased performance is automatically incorporated into the Multi\-Region Access Point\. 

**Topics**
+ [Multi\-Region Access Point hostnames](#MultiRegionAccessPointHostnames)
+ [Multi\-Region Access Points and Amazon S3 Transfer Acceleration](#MultiRegionAccessPointsAndTransferAcceleration)
+ [Multi\-Region Access Point permissions](MultiRegionAccessPointPermissions.md)
+ [Multi\-Region Access Point request routing](MultiRegionAccessPointRequestRouting.md)
+ [Configuring bucket replication for use with Multi\-Region Access Points](MultiRegionAccessPointBucketReplication.md)
+ [Multi\-Region Access Point supported operations](MrapOperations.md)