# Setting up AWS Identity and Access Management with S3 on Outposts<a name="S3OutpostsIAM"></a>

AWS Identity and Access Management \(IAM\) is an AWS service that administrators can use to securely control access to AWS Outposts resources\. To allow IAM users to manage AWS Outposts resources, you create an IAM policy that explicitly grants them permissions\.You then attach the policy to the IAM users or groups that require those permissions\. For more information, see [Identity and Access Management for AWS Outposts](https://docs.aws.amazon.com/outposts/latest/userguide/identity-access-management.html) in the *AWS Outposts User Guide*\. 

Amazon S3 on Outposts supports both bucket and access point policies\. S3 on Outposts policies use a different IAM actions namespace from S3 \(`s3-outposts:*` vs\. `s3:*`\) to provide you with distinct controls for data stored on your Outpost\.

Requests made to S3 on Outposts control API in an AWS Region and requests made to the object API endpoints on the Outpost are authenticated using IAM and authorized against the `s3-outposts:*` IAM namespace\. 

 Configure your IAM users and authorize them against the `s3-outposts:*` IAM namespace\. Access point policies and bucket policies that are configured on the Outpost access point control authorization of object API requests in addition to IAM user policies\.

**Note**  
Access control lists \(ACLs\) are not supported on S3 on Outposts\.
S3 on Outposts defaults to the bucket owner as object owner, to help ensure that the owner of a bucket can't be prevented from accessing or deleting objects\.
S3 on Outposts always has S3 Block Public Access enabled to help ensure that objects can never have public access\.
S3 on Outposts uses the IAM actions namespace `s3-outposts:<ACTION>`\. For more information, see [Actions, resources, and condition keys for S3 on Outposts](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazons3onoutposts.html) in the *Service Authorization Reference*\.

For more information about setting up IAM for S3 on Outposts, see the following topics\.

**Topics**
+ [ARNs for S3 on Outposts](#S3OutpostsARN)
+ [Permissions for S3 on Outposts endpoints](#S3OutpostsEndpointPermissions)

## ARNs for S3 on Outposts<a name="S3OutpostsARN"></a>

Amazon Resource Names \(ARNs\) for S3 on Outposts contain the Outpost ID in addition to AWS Region, AWS account ID, and resource name\. You must use this ARN format to access and perform actions on your Outposts buckets and objects\.


****  

| Amazon S3 on Outposts ARN | ARN format | Example | 
| --- | --- | --- | 
| Bucket ARN | arn:<partition>:s3\-outposts:<region>:​<account\_id>:​outpost/<outpost\_id>/bucket/<bucket\_name> | arn:aws:s3\-outposts:us\-west\-2:123456789012:​outpost/op\-01ac5d28a6a232904/bucket/DOC\-EXAMPLE\-BUCKET1 | 
| Access point ARN | arn:<partition>:s3\-outposts:<region>:​<account\_id>:​outpost/<outpost\_id>/accesspoint/<accesspoint\_name> | arn:aws:s3\-outposts:us\-west\-2:123456789012:​outpost/op\-01ac5d28a6a232904/accesspoint/access\-point\-name | 
| Object ARN | arn:<partition>:s3\-outposts:<region>:​<account\_id>:​outpost/<outpost\_id>/bucket/<bucket\_name>/object/<object\_key> | arn:aws:s3\-outposts:us\-west\-2:123456789012:​outpost/op\-01ac5d28a6a232904/bucket/DOC\-EXAMPLE\-BUCKET1/object/myobject | 
| S3 on Outposts AP object ARN \(used in policies\) | arn:<partition>:s3\-outposts:<region>:​<account\_id>:​outpost/<outpost\_id>/accesspoint/<accesspoint\_name>/object/<object\_key> | arn:aws:s3\-outposts:us\-west\-2:123456789012:​outpost/op\-01ac5d28a6a232904/accesspoint/access\-point\-name/object/myobject | 
| S3 on Outposts ARN | arn:<partition>:s3\-outposts:<region>:​<account\_id>:​outpost/<outpost\_id> | arn:aws:s3\-outposts:us\-west\-2:123456789012:​outpost/op\-01ac5d28a6a232904 | 

## Permissions for S3 on Outposts endpoints<a name="S3OutpostsEndpointPermissions"></a>

For endpoints that use the customer\-owned IP address pool \(CoIP pool\) access type, you also must have permissions to allocate and associate IP addresses from your CoIP pool\. 

S3 on Outposts requires its own permissions in IAM to manage S3 on Outposts endpoint actions\.


**S3 on Outposts endpoint\-related IAM permissions**  

| Action | IAM permissions | 
| --- | --- | 
| CreateEndpoint |  `s3-outposts:CreateEndpoint` `ec2:CreateNetworkInterface` `ec2:DescribeNetworkInterfaces` `ec2:DescribeVpcs` `ec2:DescribeSecurityGroups` `ec2:DescribeSubnets` `ec2:CreateTags` For endpoints that are using the on\-premises customer\-owned IP address pool \(CoIP pool\) access type, the following additional permissions are required: `s3-outposts:CreateEndpoint` `ec2:DescribeCoipPools` `ec2:GetCoipPoolUsage` `ec2:AllocateAddress` `ec2:AssociateAddress` `ec2:DescribeAddresses` `ec2:DescribeLocalGatewayRouteTableVpcAssociations`  | 
| DeleteEndpoint |  `s3-outposts:DeleteEndpoint` `ec2:DeleteNetworkInterface` `ec2:DescribeNetworkInterfaces` For endpoints that are using the on\-premises customer\-owned IP address pool \(CoIP pool\) access type, the following additional permissions are required: `s3-outposts:DeleteEndpoint` `ec2:DisassociateAddress` `ec2:DescribeAddresses` `ec2:ReleaseAddress`  | 
| ListEndpoints |  `s3-outposts:ListEndpoints`  | 

**Note**  
You can use resource tags in an IAM policy to manage permissions\.

For shared accounts accessing S3 on Outposts using AWS Resource Access Manager, users can't create their own endpoints on a shared subnet\. If a user in a shared account wants to manage their own endpoints, the shared account must create its own subnet on the Outpost\.