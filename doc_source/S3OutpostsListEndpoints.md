# Viewing a list of your Amazon S3 on Outposts endpoints<a name="S3OutpostsListEndpoints"></a>

To route requests to an Amazon S3 on Outposts access point, you must create and configure an S3 on Outposts endpoint\. Each virtual private cloud \(VPC\) on your Outpost can have one associated endpoint\. For more information about endpoint quotas, see [ S3 on Outposts network requirements](S3OnOutpostsRestrictionsLimitations.md#S3OnOutpostsConnectivityRestrictions)\. You must create these endpoints to be able to access your Outposts buckets and perform object operations\. For more information, see [Endpoints](S3OutpostsWorkingBuckets.md#S3OutpostsEP)\.

The following examples show you how to return a list of your S3 on Outposts endpoints by using the AWS Management Console, AWS Command Line Interface \(AWS CLI\), and AWS SDK for Java\.

## Using the S3 console<a name="s3-outposts-endpoint-list"></a>

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Outposts access points**\.

1. On the **Outposts access points** page, choose the **Outposts endpoints** tab\.

1. Under **Outposts endpoints**, you can view a list of your S3 on Outposts endpoints\.

## Using the AWS CLI<a name="S3OutpostsListEndpointsCLI"></a>

The following AWS CLI example lists the endpoints for the AWS Outposts resources that are associated with your account\. For more information about this command, see [list\-endpoints](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3outposts/list-endpoints.html) in the *AWS CLI Reference*\.

```
aws s3outposts list-endpoints
```

## Using the AWS SDK for Java<a name="S3OutpostsListEndpointsJava"></a>

The following SDK for Java example lists the endpoints for an Outpost\. For more information, see [ListEndpoints](https://docs.aws.amazon.com/AmazonS3/latest/API/API_s3outposts_ListEndpoints.html) in the *Amazon Simple Storage Service API Reference*\.

```
import com.amazonaws.services.s3outposts.AmazonS3Outposts;
import com.amazonaws.services.s3outposts.AmazonS3OutpostsClientBuilder;
import com.amazonaws.services.s3outposts.model.ListEndpointsRequest;
import com.amazonaws.services.s3outposts.model.ListEndpointsResult;

public void listEndpoints() {
    AmazonS3Outposts s3OutpostsClient = AmazonS3OutpostsClientBuilder
                .standard().build();
                
    ListEndpointsRequest listEndpointsRequest = new ListEndpointsRequest();
    ListEndpointsResult listEndpointsResult = s3OutpostsClient.listEndpoints(listEndpointsRequest);
    System.out.println("List endpoints result is " + listEndpointsResult);
}
```