# Configuring a Multi\-Region Access Point for use with AWS PrivateLink<a name="MultiRegionAccessPointsPrivateLink"></a>

 AWS PrivateLink provides you with private connectivity to Amazon S3 using private IP addresses in your virtual private cloud \(VPC\)\. You can provision one or more interface endpoints inside your VPC to connect to Amazon S3 Multi\-Region Access Points\.

 You can create **com\.amazonaws\.s3\-global\.accesspoint** endpoints for Multi\-Region Access Points through the AWS Management Console, AWS CLI, or AWS SDKs\. To learn more about how to configure an interface endpoint for Multi\-Region Access Point, see [Interface VPC endpoints](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-interface.html) in the *VPC User Guide*\. 

 To make requests to a Multi\-Region Access Point via interface endpoints, follow these steps to configure the VPC and the Multi\-Region Access Point\. 

**To configure a Multi\-Region Access Point to use with AWS PrivateLink**

1. Create or have an appropriate VPC endpoint that can connect to Multi\-Region Access Points\. For more information about creating VPC endpoints, see [Interface VPC endpoints](https://docs.aws.amazon.com/vpc/latest/privatelink/vpce-interface.html) in the *VPC User Guide*\.
**Important**  
 Make sure to create a **com\.amazonaws\.s3\-global\.accesspoint** endpoint\. Other endpoint types cannot access Multi\-Region Access Points\. 

   After this VPC endpoint is created, all Multi\-Region Access Point requests in the VPC route through this endpoint if you have private DNS enabled for the endpoint\. This is enabled by default\. 

1. If the Multi\-Region Access Point policy does not support connections from VPC endpoints, you will need to update it\.

1. Verify that the individual bucket policies will allow access to the users of the Multi\-Region Access Point\.

 Remember that Multi\-Region Access Points work by routing requests to buckets, not by fulfilling requests themselves\. This is important to remember because the originator of the request must have permissions to the Multi\-Region Access Point and be allowed to access the individual buckets in the Multi\-Region Access Point\. Otherwise, the request might be routed to a bucket where the originator doesn't have permissions to fulfill the request\. A Multi\-Region Access Point and the buckets must be owned by the same AWS account\. However, VPCs from different accounts can use a Multi\-Region Access Point if the permissions are configured correctly\. 

 Because of this, the VPC endpoint policy must allow access both to the Multi\-Region Access Point and to each underlying bucket that you want to be able to fulfill requests\. For example, suppose that you have a Multi\-Region Access Point with alias `mfzwi23gnjvgw.mrap`\. It is backed by buckets `doc-examplebucket1` and `doc-examplebucket2`, all owned by AWS account `123456789012`\. In this case, the following VPCE policy would allow `GetObject` requests from the VPC made to `mfzwi23gnjvgw.mrap` to be fulfilled by either backing bucket\. 

```
 1. {
 2.     "Version": "2012-10-17",
 3.     "Statement": [
 4.     {
 5.         "Sid": "Read-buckets-and-MRAP-VPCE-policy",
 6.         "Principal": "*",
 7.         "Action": [
 8.             "s3:GetObject"
 9.         ],
10.         "Effect": "Allow",
11.         "Resource": [
12.             "arn:aws:s3:::doc-examplebucket1/*",
13.             "arn:aws:s3:::doc-examplebucket2/*",
14.             "arn:aws:s3::123456789012:accesspoint/mfzwi23gnjvgw.mrap/object/*"
15.         ]
16.     }]
17. }
```

 As mentioned previously, you also must make sure that the Multi\-Region Access Point policy is configured to support access through a VPC endpoint\. You don't need to specify the VPC endpoint that is requesting access\. The following sample policy would grant access to any requestor trying to use the Multi\-Region Access Point for the `GetObject` requests\. 

```
 1. {
 2.     "Version": "2012-10-17",
 3.     "Statement": [
 4.     {
 5.         "Sid": "Open-read-MRAP-policy",
 6.         "Effect": "Allow",
 7.         "Principal": "*",
 8.         "Action": [
 9.             "s3:GetObject"
10.           ],
11.         "Resource": "arn:aws:s3::123456789012:accesspoint/mfzwi23gnjvgw.mrap/object/*"
12.     }]
13. }
```

 And of course, the individual buckets would each need a policy to support access from requests submitted through VPC endpoint\. The following example policy grants read access to any anonymous users, which would include requests made through the VPC endpoint\. 

```
 1. {
 2.     "Version":"2012-10-17",
 3.    "Statement": [
 4.    {
 5.        "Sid": "Public-read",
 6.        "Effect":"Allow",
 7.        "Principal": "*",
 8.        "Action": "s3:GetObject",
 9.        "Resource": [
10.            "arn:aws:s3:::doc-examplebucket1",
11.            "arn:aws:s3:::doc-examplebucket2/*"]
12.     }]
13. }
```

 For more information about editing a VPCE policy, see [Control access to services with VPC endpoints](https://docs.aws.amazon.com/vpc/latest/privatelink/vpc-endpoints-access.html) in the *VPC User Guide*\. 

## Removing access to a Multi\-Region Access Point from a VPC endpoint<a name="RemovingMultiRegionAccessPointAccess"></a>

 If you own a Multi\-Region Access Point and want to remove access to it from an interface endpoint, you must supply a new access policy to the Multi\-Region Access Point that prevents access for requests coming through VPC endpoints\. Keep in mind that if the buckets in your Multi\-Region Access Point support requests through VPC endpoints, they will continue to support these requests\. If you want to prevent that support, you must also update the policies for the buckets\. Supplying a new access policy to the Multi\-Region Access Point only prevents access to the Multi\-Region Access Point\. 

**Note**  
 You can't delete an access policy for a Multi\-Region Access Point\. To remove access to a Multi\-Region Access Point, you must provide a new access policy with the modified access that you want\. 

 As an alternative, you could update the bucket policies to prevent requests through VPC endpoints\. In this case, the user could still access the Multi\-Region Access Point through the VPC endpoint\. But if the request is routed to a bucket where the bucket policy prevents access, it would generate an error message\. 