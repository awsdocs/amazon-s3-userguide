# Creating an endpoint on an Outpost<a name="S3OutpostsCreateEndpoint"></a>

To route requests to an S3 on Outposts access point, you must create and configure an S3 on Outposts endpoint\. Each virtual private cloud \(VPC\) on your Outpost can have one associated endpoint\. For more information about endpoint quotas, see [ S3 on Outposts network requirements](S3OnOutpostsRestrictionsLimitations.md#S3OnOutpostsConnectivityRestrictions)\. You must create these endpoints to be able to access your Outposts buckets and perform object operations\. For more information, see [Endpoints](S3OutpostsWorkingBuckets.md#S3OutpostsEP)\.

**Permissions**For more information about the permissions required to create an endpoint see [Permissions for S3 on Outposts endpoints](S3OutpostsIAM.md#S3OutpostsEndpointPermissions)\.

The following examples show you how to create an S3 on Outposts endpoint using the AWS CLI and AWS SDK for Java\. 

## Using the S3 console<a name="S3OutpostsCreateEndpointConsole"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Outposts access points**\.

1. Choose the **Outposts endpoints** tab\.

1. Choose **Create Outposts endpoint**\.

1. Under **Outpost**, choose the Outpost to create this endpoint\.

1. Under **VPC**, choose a VPC that does not yet have an endpoint and complies with the rules for Outposts endpoints\.

   A virtual private cloud \(VPC\) enables you to launch AWS resources into a virtual network that you define\. This virtual network closely resembles a traditional network that you would operate in your own data center, with the benefits of using the scalable infrastructure of AWS\. 

   If you don’t have a VPC, choose **Create VPC**\. For more information, see [Creating access points restricted to a virtual private cloud](access-points-vpc.md)\.

1. Choose **Create Outposts endpoint**\.

## Using the AWS CLI<a name="S3OutpostsCreateEndpointCLI"></a>

**Example**  
The following AWS CLI example creates an endpoint for an Outpost using the AWS VPC resource access type\. The VPC is derived from the subnet\.  

```
aws s3outposts create-endpoint --outpost-id op-01ac5d28a6a232904 --subnet-id subnet-8c7a57c5 --security-group-id sg-ab19e0d1
```
The following AWS CLI example creates an endpoint for an Outpost using the customer\-owned IP address pool \(CoIP pool\) access type\.  

```
aws s3outposts create-endpoint --outpost-id op-01ac5d28a6a232904 --subnet-id subnet-8c7a57c5 --security-group-id sg-ab19e0d1 --access-type CustomerOwnedIp --customer-owned-ipv4-pool ipv4pool-coip-12345678901234567
```

## Using the AWS SDK for Java<a name="S3OutpostsCreateEndpointJava"></a>

**Example**  
The following SDK for Java example creates an endpoint for an Outpost\.  

```
import com.amazonaws.services.s3outposts.AmazonS3Outposts;
import com.amazonaws.services.s3outposts.AmazonS3OutpostsClientBuilder;
import com.amazonaws.services.s3outposts.model.CreateEndpointRequest;
import com.amazonaws.services.s3outposts.model.CreateEndpointResult;

public void createEndpoint() {
    AmazonS3Outposts s3OutpostsClient = AmazonS3OutpostsClientBuilder
                .standard().build();
                
    CreateEndpointRequest createEndpointRequest = new CreateEndpointRequest()
                .withOutpostId("op-0d79779cef3c30a40")
                .withSubnetId("subnet-8c7a57c5")
                .withSecurityGroupId("sg-ab19e0d1")		
                .withAccessType("CustomerOwnedIp")   
                .withCustomerOwnedIpv4Pool("ipv4pool-coip-12345678901234567");
    // Use .withAccessType and .withCustomerOwnedIpv4Pool only when the access type is 
    // customer-owned IP address pool (CoIP pool) 
    CreateEndpointResult createEndpointResult = s3OutpostsClient.createEndpoint(createEndpointRequest);
    System.out.println("Endpoint is created and its ARN is " + createEndpointResult.getEndpointArn());
}
```