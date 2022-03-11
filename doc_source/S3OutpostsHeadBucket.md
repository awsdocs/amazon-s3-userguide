# Using HeadBucket to determine if an S3 on Outposts bucket exists and you have access permissions<a name="S3OutpostsHeadBucket"></a>

Objects are the fundamental entities stored in S3 on Outposts\. Every object is contained in a bucket\. You must use access points to access any object in an Outpost bucket\. When you specify the bucket for object operations, you use the access point Amazon Resource Name \(ARN\), which includes the AWS Region code for the Region the Outpost is homed to, AWS account ID, Outpost ID, and access point name\. The following example shows the ARN format for S3 on Outposts access points in object operations:

```
arn:aws:s3-outposts:region:account-id:outpost/outpost-id/accesspoint/accesspoint-name
```

**Note**  
With S3 on Outposts, object data is always stored on the Outpost\. When AWS installs an Outpost rack, your data stays local to your Outpost to meet data residency requirements\. Your objects never leave your Outpost and are not in an AWS Region\. Because the AWS Management Console is hosted in\-Region, you can't use the console to upload or manage objects in your Outpost\. However, you can use the REST API, AWS CLI, and AWS SDKs to upload and manage your objects through your access points\.

The following AWS Command Line Interface \(AWS CLI\) and AWS SDK for Java examples show you how to use HeadBucket to determine if an Amazon S3 on Outposts bucket exists and you have permission to access it\. For more information, see the [Head Bucket](https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadBucket.html) in the *Amazon Simple Storage Service API Reference*\.

## Using the AWS CLI<a name="S3OutpostsHeadBucketCLI"></a>

The following S3 on Outposts AWS CLI example uses `head-bucket` to determine if a bucket exists and you have permissions to access it\.

```
aws s3api head-bucket --bucket arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/accesspoint/example-Outposts-Access-Point
```

**Note**  
When using this action with Amazon S3 on Outposts through the AWS SDKs, you provide the Outposts access point ARN in place of the bucket name, in the following form: `arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/accesspoint/example-Outposts-Access-Point`\. For more information about S3 on Outposts ARNs, see [ARNs for S3 on Outposts](S3OutpostsIAM.md#S3OutpostsARN)\.

## Using the AWS SDK for Java<a name="S3OutpostsHeadBucketJava"></a>

The following S3 on Outposts example shows how to determine if a bucket exists and if you have permission to access it\.

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.HeadBucketRequest;

public class HeadBucket {
    public static void main(String[] args) {
        String accessPointArn = "*** access point ARN ***";

        try {
            // This code expects that you have AWS credentials set up per:
            // https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .enableUseArnRegion()
                    .build();

            s3Client.headBucket(new HeadBucketRequest(accessPointArn));
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