# Setting up IAM with S3 on Outposts<a name="S3OutpostsIAM"></a>

AWS Identity and Access Management \(IAM\) is an AWS service that helps an administrator securely control access to AWS resources\. IAM administrators control who can be authenticated \(signed in\) and authorized \(have permissions\) to use Amazon S3 on Outposts resources\. IAM is an AWS service that you can use with no additional charge\. By default, users don't have permissions for S3 on Outposts resources and operations\. To grant access permissions for S3 on Outposts resources and API operations, you can use IAM to create [users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users.html), [groups](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_groups.html), or [roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html#id_iam-roles) and attach permissions\.

To provide access, add permissions to your users, groups, or roles:
+ Users and groups in AWS IAM Identity Center \(successor to AWS Single Sign\-On\):

  Create a permission set\. Follow the instructions in [Create a permission set](https://docs.aws.amazon.com/singlesignon/latest/userguide/howtocreatepermissionset.html) in the *AWS IAM Identity Center \(successor to AWS Single Sign\-On\) User Guide*\.
+ Users managed in IAM through an identity provider:

  Create a role for identity federation\. Follow the instructions in [Creating a role for a third\-party identity provider \(federation\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-idp.html) in the *IAM User Guide*\.
+ IAM users:
  + Create a role that your user can assume\. Follow the instructions in [Creating a role for an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.
  + \(Not recommended\) Attach a policy directly to a user or add a user to a user group\. Follow the instructions in [Adding permissions to a user \(console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html#users_change_permissions-add-console) in the *IAM User Guide*\.

In addition to IAM identity\-based policies, S3 on Outposts supports both bucket and access point policies\. Bucket policies and access point policies are [resource\-based policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_identity-vs-resource.html) that are attached to the S3 on Outposts resource\. 
+ A bucket policy is attached to the bucket and allows or denies requests to the bucket and the objects in it based on the elements in the policy\. 
+ In contrast, an access point policy is attached to the access point and allows or denies requests to the access point\.

The access point policy works with the bucket policy that is attached to the underlying S3 on Outposts bucket\. For an application or user to access objects in an S3 on Outposts bucket through an S3 on Outposts access point, both the access point policy and the bucket policy must permit the request\. 

Restrictions that you include in an access point policy apply only to requests made through that access point\. For example, if an access point is attached to a bucket, you can't use the access point policy to allow or deny requests that are made directly to the bucket\. However, restrictions that you apply to a bucket policy can allow or deny requests made directly to the bucket or through the access point\. 

In an IAM policy or a resource\-based policy, you define which S3 on Outposts actions are allowed or denied\. S3 on Outposts actions correspond to specific S3 on Outposts API operations\. S3 on Outposts actions use the `s3-outposts:` namespace prefix\. Requests made to the S3 on Outposts control API in an AWS Region and requests made to the object API endpoints on the Outpost are authenticated by using IAM and authorized against the `s3-outposts:` namespace prefix\. To work with S3 on Outposts, configure your IAM users and authorize them against the `s3-outposts:` IAM namespace\.

For more information, see [Actions, resources, and condition keys for Amazon S3 on Outposts](https://docs.aws.amazon.com/service-authorization/latest/reference/list_amazons3onoutposts.html) in the *Service Authorization Reference*\.

**Note**  
Access control lists \(ACLs\) are not supported by S3 on Outposts\.
S3 on Outposts defaults to the bucket owner as object owner to help ensure that the owner of a bucket can't be prevented from accessing or deleting objects\.
S3 on Outposts always has S3 Block Public Access enabled to help ensure that objects can never have public access\.

For more information about setting up IAM for S3 on Outposts, see the following topics\.

**Topics**
+ [Principals for S3 on Outposts policies](#S3OutpostsPrincipal)
+ [Resource ARNs for S3 on Outposts](#S3OutpostsARN)
+ [Example policies for S3 on Outposts](#S3OutpostsPolicyExamples)
+ [Permissions for S3 on Outposts endpoints](#S3OutpostsEndpointPermissions)

## Principals for S3 on Outposts policies<a name="S3OutpostsPrincipal"></a>

When you create a resource\-based policy to grant access to your S3 on Outposts bucket, you must use the `Principal` element to specify the person or application that can make a request for an action or operation on that resource\. For S3 on Outposts policies, you can use one of the following principals:
+ An AWS account
+ An IAM user
+ An IAM role
+ All principals, by specifying a wildcard character \(\*\) in a policy that uses a `Condition` element to limit access to a specific IP range

**Important**  
You can't write a policy for an S3 on Outposts bucket that uses a wildcard character \(`*`\) in the `Principal` element unless the policy also includes a `Condition` that limits access to a specific IP address range\. This restriction helps ensure that there is no public access to your S3 on Outposts bucket\. For an example, see [Example policies for S3 on Outposts](#S3OutpostsPolicyExamples)\. 

For more information about the `Principal` element, see [AWS JSON policy elements: Principal](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_principal.html) in the *IAM User Guide*\. 

## Resource ARNs for S3 on Outposts<a name="S3OutpostsARN"></a>

Amazon Resource Names \(ARNs\) for S3 on Outposts contain the Outpost ID in addition to the AWS Region that the Outpost is homed to, the AWS account ID, and the resource name\. To access and perform actions on your Outposts buckets and objects, you must use one of the ARN formats shown in the following table\.

The `partition` value in the ARN refers to a group of AWS Regions\. Each AWS account is scoped to one partition\. The following are the supported partitions:
+ `aws` – AWS Regions
+ `aws-us-gov` – AWS GovCloud \(US\) Regions


**S3 on Outposts ARN formats**  

| Amazon S3 on Outposts ARN | ARN format | Example | 
| --- | --- | --- | 
| Bucket ARN | arn:partition:s3\-outposts:region:​account\_id:​outpost/outpost\_id/bucket/bucket\_name | arn:aws:s3\-outposts:us\-west\-2:123456789012:​outpost/op\-01ac5d28a6a232904/bucket/DOC\-EXAMPLE\-BUCKET1 | 
| Access point ARN | arn:partition:s3\-outposts:region:​account\_id:​outpost/outpost\_id/accesspoint/accesspoint\_name | arn:aws:s3\-outposts:us\-west\-2:123456789012:​outpost/op\-01ac5d28a6a232904/accesspoint/access\-point\-name | 
| Object ARN | arn:partition:s3\-outposts:region:​account\_id:​outpost/outpost\_id/bucket/bucket\_name/object/object\_key | arn:aws:s3\-outposts:us\-west\-2:123456789012:​outpost/op\-01ac5d28a6a232904/bucket/DOC\-EXAMPLE\-BUCKET1/object/myobject | 
| S3 on Outposts access point object ARN \(used in policies\) | arn:partition:s3\-outposts:region:​account\_id:​outpost/outpost\_id/accesspoint/accesspoint\_name/object/object\_key | arn:aws:s3\-outposts:us\-west\-2:123456789012:​outpost/op\-01ac5d28a6a232904/accesspoint/access\-point\-name/object/myobject | 
| S3 on Outposts ARN | arn:partition:s3\-outposts:region:​account\_id:​outpost/outpost\_id | arn:aws:s3\-outposts:us\-west\-2:123456789012:​outpost/op\-01ac5d28a6a232904 | 

## Example policies for S3 on Outposts<a name="S3OutpostsPolicyExamples"></a>

**Example : S3 on Outposts bucket policy with an AWS account principal**  
The following bucket policy uses an AWS account principal to grant access to an S3 on Outposts bucket\. To use this bucket policy, replace the `user input placeholders` with your own information\.  

```
{
   "Version":"2012-10-17",
   "Id":"ExampleBucketPolicy1",
   "Statement":[
      {
         "Sid":"statement1",
         "Effect":"Allow",
         "Principal":{
            "AWS":"123456789012"
         },
         "Action":"s3-outposts:*",
         "Resource":"arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/bucket/example-outposts-bucket"
      }
   ]
}
```

**Example : S3 on Outposts bucket policy with a wildcard principal \(`*`\) and condition key to limit access to a specific IP address range**  
The following bucket policy uses a wildcard principal \(`*`\) with the `aws:SourceIp` condition to limit access to a specific IP address range\. To use this bucket policy, replace the `user input placeholders` with your own information\.  

```
{
    "Version": "2012-10-17",
    "Id": "ExampleBucketPolicy2",
    "Statement": [
        {
            "Sid": "statement1",
            "Effect": "Allow",
            "Principal": { "AWS" : "*" },
            "Action":"s3-outposts:*",
            "Resource":"arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/bucket/example-outposts-bucket",
            "Condition" : {
                "IpAddress" : {
                    "aws:SourceIp": "192.0.2.0/24" 
                },
                "NotIpAddress" : {
                    "aws:SourceIp": "198.51.100.0/24" 
                } 
            } 
        } 
    ]
}
```

## Permissions for S3 on Outposts endpoints<a name="S3OutpostsEndpointPermissions"></a>

 

S3 on Outposts requires its own permissions in IAM to manage S3 on Outposts endpoint actions\.

**Note**  
For endpoints that use the customer\-owned IP address pool \(CoIP pool\) access type, you also must have permissions to work with IP addresses from your CoIP pool, as described in the following table\.
For shared accounts that access S3 on Outposts by using AWS Resource Access Manager, users in these shared accounts can't create their own endpoints on a shared subnet\. If a user in a shared account wants to manage their own endpoints, the shared account must create its own subnet on the Outpost\. For more information, see [Sharing S3 on Outposts by using AWS RAM](outposts-sharing-with-ram.md)\.


**S3 on Outposts endpoint\-related IAM permissions**  

| Action | IAM permissions | 
| --- | --- | 
| CreateEndpoint |  `s3-outposts:CreateEndpoint` `ec2:CreateNetworkInterface` `ec2:DescribeNetworkInterfaces` `ec2:DescribeVpcs` `ec2:DescribeSecurityGroups` `ec2:DescribeSubnets` `ec2:CreateTags` For endpoints that are using the on\-premises customer\-owned IP address pool \(CoIP pool\) access type, the following additional permissions are required: `s3-outposts:CreateEndpoint` `ec2:DescribeCoipPools` `ec2:GetCoipPoolUsage` `ec2:AllocateAddress` `ec2:AssociateAddress` `ec2:DescribeAddresses` `ec2:DescribeLocalGatewayRouteTableVpcAssociations`  | 
| DeleteEndpoint |  `s3-outposts:DeleteEndpoint` `ec2:DeleteNetworkInterface` `ec2:DescribeNetworkInterfaces` For endpoints that are using the on\-premises customer\-owned IP address pool \(CoIP pool\) access type, the following additional permissions are required: `s3-outposts:DeleteEndpoint` `ec2:DisassociateAddress` `ec2:DescribeAddresses` `ec2:ReleaseAddress`  | 
| ListEndpoints |  `s3-outposts:ListEndpoints`  | 

**Note**  
You can use resource tags in an IAM policy to manage permissions\.