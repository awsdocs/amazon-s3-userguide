# Sharing Amazon S3 on Outposts using AWS Resource Access Manager<a name="outposts-sharing-with-ram"></a>

Amazon S3 on Outposts supports sharing S3 capacity across multiple accounts within an organization by using AWS Resource Access Manager \([AWS RAM](https://docs.aws.amazon.com/ram/latest/userguide/what-is.html)\)\. With S3 on Outposts sharing, you can allow others to create and manage buckets, endpoints, and access points on your Outpost\. 

This topic demonstrates how to use AWS RAM to share S3 on Outposts and related resources with another AWS account in your AWS organization\. 

## Prerequisites<a name="outposts-ram-prereqs"></a>
+ The Outpost owner account has an AWS organization configured\. For more information, see [ Creating an organization](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_org_create.html) in the *AWS Organizations User Guide*\.
+ The AWS organization includes the AWS account that you want to share your S3 on Outposts capacity with\. For more information, see [ Sending invitations to AWS accounts](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_accounts_invites.html#orgs_manage_accounts_invite-account) in the *AWS Organizations User Guide*\.
+ Select one of the following options that you want to share\. The second resource \(either Subnets or Outposts\) must be selected so that endpoints are also accessible\. Endpoints are a networking requirement in order to access data stored in S3 on Outposts\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/outposts-sharing-with-ram.html)

## Procedure<a name="outposts-ram-procedure"></a>

1. Sign in to the AWS Management Console using the AWS account that owns the Outpost, and then open the AWS RAM console at [https://console\.aws\.amazon\.com/ram](https://console.aws.amazon.com/ram/)\.

1. Ensure that you have enabled sharing with AWS Organizations in AWS RAM\. For information, see [ Enable resource sharing within AWS Organizations](https://docs.aws.amazon.com/ram/latest/userguide/getting-started-sharing.html#getting-started-sharing-orgs) in the *AWS RAM User Guide*\.

1. Use the option that you selected in the [prerequisites](#outposts-ram-prereqs) to create a resource share\. If you have multiple S3 on Outposts, select the Amazon Resource Names \(ARNs\) of the resources that you want to share\. To enable endpoints, share either your Subnet or Outpost\.

   For more information about how to create a resource share, see [ Create a resource share](https://docs.aws.amazon.com/ram/latest/userguide/getting-started-sharing.html#getting-started-sharing-create) in the *AWS RAM User Guide*\. 

1. The AWS account that you shared your resources with should now be able to use S3 on Outposts\. Depending on the option that you selected in the [prerequisites](#outposts-ram-prereqs), provide the following information to the account user:    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/outposts-sharing-with-ram.html)

**Note**  
The user can confirm that the resources have been shared with them using the AWS RAM console, the AWS Command Line Interface \(AWS CLI\), AWS SDKs, or REST API\. The user can view their existing resource shares by using the [get\-resource\-shares](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/ram/get-resource-shares.html) CLI command\. 

## Usage examples<a name="outposts-ram-examples"></a>

After you have shared your S3 on Outposts resources with another account, that account can manage buckets and objects on your Outpost\. If you shared the **Subnets** resource, then that account can use the endpoint you created\. The following examples demonstrate how a user can use the AWS CLI to interact with your Outpost after you share these resources\.

**Example : Create a bucket**  
The following example creates a bucket named *DOC\-EXAMPLE\-BUCKET1* on the Outpost `op-01ac5d28a6a232904`\. Before using this command, replace each `user input placeholder` with the appropriate values for your use case\.  

```
aws s3control create-bucket --bucket DOC-EXAMPLE-BUCKET1 --outpost-id op-01ac5d28a6a232904
```

**Example : Create an access point**  
The following example creates an access point on an Outpost, using the example parameters in the following table\. Before using this command, replace these `user input placeholder` values and the AWS Region code with the appropriate values for your use case\.  


| **Parameter** | **Value** | 
| --- | --- | 
| Account ID | 111122223333 | 
| Access point name | example\-outpost\-access\-point | 
| Outpost ID | op\-01ac5d28a6a232904 | 
| Outpost bucket name | DOC\-EXAMPLE\-BUCKET1 | 
| VPC ID | vpc\-1a2b3c4d5e6f7g8h9 | 
The Account ID parameter must be the AWS account ID of the bucket owner, which is the shared user\.

```
aws s3control create-access-point --account-id 111122223333 --name example-outpost-access-point \
--bucket arn:aws:s3-outposts:us-east-1:111122223333:outpost/op-01ac5d28a6a232904/bucket/DOC-EXAMPLE-BUCKET1 \
--vpc-configuration VpcId=vpc-1a2b3c4d5e6f7g8h9
```

**Example : Upload an object**  
The following example uploads the file *`my_image.jpg`* from the user's local file system to an object named *`images/my_image.jpg`* through the access point *`example-outpost-access-point`* on the Outpost *`op-01ac5d28a6a232904`*, owned by the AWS account *`111122223333`*\. Before using this command, replace these `user input placeholder` values and the AWS Region code with the appropriate values for your use case\.  

```
aws s3api put-object --bucket arn:aws:s3-outposts:us-east-1:111122223333:outpost/op-01ac5d28a6a232904/accesspoint/example-outpost-access-point \
--body my_image.jpg --key images/my_image.jpg
```
If this operation results in a Resource not found error or is unresponsive, your VPC might not have a shared endpoint\.   
To check whether there is a shared endpoint, use the [list\-shared\-endpoints](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3outposts/list-shared-endpoints.html) AWS CLI command\. If there is no shared endpoint, work with the Outpost owner to create one\. For more information see [ListSharedEndpoints](https://docs.aws.amazon.com/AmazonS3/latest/API/API_s3outposts_ListSharedEndpoints.html) in the *Amazon Simple Storage Service API Reference*\.

**Example : Create an endpoint**  
The following example creates an endpoint on a shared Outpost\. Before using this command, replace the Outpost ID, subnet ID, and security group ID with the appropriate values for your use case\.  
The user can perform this operation only if the resource share includes the **Outposts** resource\.

```
aws s3outposts create-endpoint --outposts-id op-01ac5d28a6a232904 --subnet-id XXXXXX --security-group-id XXXXXXX
```