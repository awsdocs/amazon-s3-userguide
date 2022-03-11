# Listing the objects in an Amazon S3 on Outposts bucket<a name="S3OutpostsListObjects"></a>

Objects are the fundamental entities stored in S3 on Outposts\. Every object is contained in a bucket\. You must use access points to access any object in an Outpost bucket\. When you specify the bucket for object operations, you use the access point Amazon Resource Name \(ARN\), which includes the AWS Region code for the Region the Outpost is homed to, AWS account ID, Outpost ID, and access point name\. The following example shows the ARN format for S3 on Outposts access points in object operations:

```
arn:aws:s3-outposts:region:account-id:outpost/outpost-id/accesspoint/accesspoint-name
```

**Note**  
With S3 on Outposts, object data is always stored on the Outpost\. When AWS installs an Outpost rack, your data stays local to your Outpost to meet data residency requirements\. Your objects never leave your Outpost and are not in an AWS Region\. Because the AWS Management Console is hosted in\-Region, you can't use the console to upload or manage objects in your Outpost\. However, you can use the REST API, AWS CLI, and AWS SDKs to upload and manage your objects through your access points\.

The following examples show you how to list the objects in an S3 on Outposts bucket using the AWS Command Line Interface \(AWS CLI\) and AWS SDK for Java\.

## Using the AWS CLI<a name="S3OutpostsListObjectsCLI"></a>

The following example lists the objects in an S3 on Outposts bucket \(`s3-outposts:ListObjectsV2`\) using the AWS CLI\. 

```
aws s3api list-objects-v2 --bucket arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/accesspoint/example-Outposts-Access-Point
```

**Note**  
When using this action with Amazon S3 on Outposts through the AWS SDKs, you provide the Outposts access point ARN in place of the bucket name, in the following form: `arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/accesspoint/example-Outposts-Access-Point`\. For more information about S3 on Outposts ARNs, see [ARNs for S3 on Outposts](S3OutpostsIAM.md#S3OutpostsARN)\.

## Using the AWS SDK for Java<a name="S3OutpostsListObjectsJava"></a>

The following S3 on Outposts example lists objects in a bucket using the SDK for Java\. 

**Important**  
This section describes the latest revision of the [ListObjects](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListObjectsV2.html) API operation\. We recommend that you use this revised API operation for application development\. For backward compatibility, Amazon S3 continues to support the prior version of this API operation\. 

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.ListObjectsV2Request;
import com.amazonaws.services.s3.model.ListObjectsV2Result;
import com.amazonaws.services.s3.model.S3ObjectSummary;

public class ListObjectsV2 {

    public static void main(String[] args) {
        String accessPointArn = "*** access point ARN ***";

        try {
            // This code expects that you have AWS credentials set up per:
            // https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .enableUseArnRegion()
                    .build();

            System.out.println("Listing objects");

            // maxKeys is set to 2 to demonstrate the use of
            // ListObjectsV2Result.getNextContinuationToken()
            ListObjectsV2Request req = new ListObjectsV2Request().withBucketName(accessPointArn).withMaxKeys(2);
            ListObjectsV2Result result;

            do {
                result = s3Client.listObjectsV2(req);

                for (S3ObjectSummary objectSummary : result.getObjectSummaries()) {
                    System.out.printf(" - %s (size: %d)\n", objectSummary.getKey(), objectSummary.getSize());
                }
                // If there are more than maxKeys keys in the bucket, get a continuation token
                // and list the next objects.
                String token = result.getNextContinuationToken();
                System.out.println("Next Continuation Token: " + token);
                req.setContinuationToken(token);
            } while (result.isTruncated());
        } catch (AmazonServiceException e) {
            // The call was transmitted successfully, but Amazon S3 couldn't process
            // it, so it returned an error response.
            e.printStackTrace();
        } catch (SdkClientException e) {
            // Amazon S3 couldn't be contacted for a response, or the client
            // couldn't parse the response from Amazon S3.
            e.printStackTrace();
        }
    }
}
```