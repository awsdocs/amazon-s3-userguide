# Deleting an Amazon S3 on Outposts endpoint<a name="S3OutpostsDeleteEndpoints"></a>

To route requests to an Amazon S3 on Outposts access point, you must create and configure an S3 on Outposts endpoint\. Each virtual private cloud \(VPC\) on your Outpost can have one associated endpoint\. For more information about endpoint quotas, see [ S3 on Outposts network requirements](S3OnOutpostsRestrictionsLimitations.md#S3OnOutpostsConnectivityRestrictions)\. You must create these endpoints to be able to access your Outposts buckets and perform object operations\. For more information, see [Endpoints](S3OutpostsWorkingBuckets.md#S3OutpostsEP)\.

The following examples show you how to delete your S3 on Outposts endpoints by using the AWS Management Console, AWS Command Line Interface \(AWS CLI\), and AWS SDK for Java\.

## Using the S3 console<a name="s3-outposts-endpoint-delete"></a>

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Outposts access points**\.

1. On the **Outposts access points** page, choose the **Outposts endpoints** tab\.

1. Under **Outposts endpoints**, choose the endpoint that you want to delete, and choose **Delete**\.

## Using the AWS CLI<a name="S3OutpostsDeleteEndpointCLI"></a>

The following AWS CLI example deletes an endpoint for an Outpost\. To run this command, replace the `user input placeholders` with your own information\.

```
aws s3outposts delete-endpoint --endpoint-id example-endpoint-id --outpost-id op-01ac5d28a6a232904
```

## Using the AWS SDK for Java<a name="S3OutpostsDeleteEndpointJava"></a>

The following SDK for Java example deletes an endpoint for an Outpost\. To use this example, replace the `user input placeholders` with your own information\.

```
import com.amazonaws.arn.Arn;
import com.amazonaws.services.s3outposts.AmazonS3Outposts;
import com.amazonaws.services.s3outposts.AmazonS3OutpostsClientBuilder;
import com.amazonaws.services.s3outposts.model.DeleteEndpointRequest;

public void deleteEndpoint(String endpointArnInput) {
    String outpostId = "op-01ac5d28a6a232904";
    AmazonS3Outposts s3OutpostsClient = AmazonS3OutpostsClientBuilder
                .standard().build();
                
    Arn endpointArn = Arn.fromString(endpointArnInput);
    String[] resourceParts = endpointArn.getResource().getResource().split("/");
    String endpointId = resourceParts[resourceParts.length - 1];
    DeleteEndpointRequest deleteEndpointRequest = new DeleteEndpointRequest()
                .withEndpointId(endpointId)
                .withOutpostId(outpostId);
    s3OutpostsClient.deleteEndpoint(deleteEndpointRequest);
    System.out.println("Endpoint with id " + endpointId + " is deleted.");
}
```