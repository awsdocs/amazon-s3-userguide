# Sharing objects by using presigned URLs<a name="S3OutpostsShareObjectPresignedURL"></a>

To grant time\-limited access to objects that are stored locally on an Outpost without updating your bucket policy, you can use a presigned URL\. With presigned URLs, you as the bucket owner can share objects with individuals in your virtual private cloud \(VPC\) or grant them the ability to upload or delete objects\. 

When you create a presigned URL by using the AWS SDKs or the AWS Command Line Interface \(AWS CLI\), you associate the URL with a specific action\. You also grant time\-limited access to the presigned URL by choosing a custom expiration time that can be as low as 1 second and as high as 7 days\. When you share the presigned URL, the individual in the VPC can perform the action embedded in the URL as if they were the original signing user\. When the URL reaches its expiration time, the URL expires and no longer works\.



When you create a presigned URL, you must provide your security credentials, and then specify the following: 
+ An access point Amazon Resource Name \(ARN\) for the Amazon S3 on Outposts bucket
+ An object key
+ An HTTP method \(`GET` for downloading objects\)
+ An expiration date and time

A presigned URL is valid only for the specified duration\. That is, you must start the action that's allowed by the URL before the expiration date and time\. You can use a presigned URL multiple times, up to the expiration date and time\. If you created a presigned URL by using a temporary token, then the URL expires when the token expires, even if you created the URL with a later expiration time\.

Users in the virtual private cloud \(VPC\) who have access to the presigned URL can access the object\. For example, if you have a video in your bucket and both the bucket and the object are private, you can share the video with others by generating a presigned URL\. Because presigned URLs grant access to your S3 on Outposts buckets to whoever has the URL, we recommend that you protect these URLs appropriately\. For more details about protecting presigned URLs, see [Limiting presigned URL capabilities](S3OutpostsPresignedURL.md#S3OutpostsPresignedUrlUploadObjectLimitCapabilities)\. 

Anyone with valid security credentials can create a presigned URL\. However, the presigned URL must be created by someone who has permission to perform the operation that the presigned URL is based upon\. For more information, see [Who can create a presigned URL](S3OutpostsPresignedURL.md#S3Outpostswho-presigned-url)\.

You can generate a presigned URL to share an object in an S3 on Outposts bucket by using the AWS SDKs and the AWS CLI\. For more information, see the following examples\. 

## Using the AWS SDKs<a name="S3OutpostsShareObjectPreSignedURLSDK"></a>

You can use the AWS SDKs to generate a presigned URL that you can give to others so that they can retrieve an object\. 

**Note**  
When you use the AWS SDKs to generate a presigned URL, the maximum expiration time for a presigned URL is 7 days from the time of creation\. 

------
#### [ Java ]

**Example**  
The following example generates a presigned URL that you can give to others so that they can retrieve an object from an S3 on Outposts bucket\. For more information, see [Using presigned URLs for S3 on Outposts](S3OutpostsPresignedURL.md)\. To use this example, replace the *`user input placeholders`* with your own information\.  
 For instructions on creating and testing a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\.   

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.HttpMethod;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.GeneratePresignedUrlRequest;

import java.io.IOException;
import java.net.URL;
import java.time.Instant;

public class GeneratePresignedURL {

    public static void main(String[] args) throws IOException {
        Regions clientRegion = Regions.DEFAULT_REGION;
        String accessPointArn = "*** access point ARN ***";
        String objectKey = "*** object key ***";

        try {
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .withRegion(clientRegion)
                    .withCredentials(new ProfileCredentialsProvider())
                    .build();

            // Set the presigned URL to expire after one hour.
            java.util.Date expiration = new java.util.Date();
            long expTimeMillis = Instant.now().toEpochMilli();
            expTimeMillis += 1000 * 60 * 60;
            expiration.setTime(expTimeMillis);

            // Generate the presigned URL.
            System.out.println("Generating pre-signed URL.");
            GeneratePresignedUrlRequest generatePresignedUrlRequest =
                    new GeneratePresignedUrlRequest(accessPointArn, objectKey)
                            .withMethod(HttpMethod.GET)
                            .withExpiration(expiration);
            URL url = s3Client.generatePresignedUrl(generatePresignedUrlRequest);

            System.out.println("Pre-Signed URL: " + url.toString());
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

------
#### [ \.NET ]

**Example**  
The following example generates a presigned URL that you can give to others so that they can retrieve an object from an S3 on Outposts bucket\. For more information, see [Using presigned URLs for S3 on Outposts](S3OutpostsPresignedURL.md)\. To use this example, replace the *`user input placeholders`* with your own information\.   
For instructions on creating and testing a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\.  

```
using Amazon;
using Amazon.S3;
using Amazon.S3.Model;
using System;

namespace Amazon.DocSamples.S3
{
    class GenPresignedURLTest
    {
        private const string accessPointArn = "*** access point ARN ***"; 
        private const string objectKey = "*** object key ***";
        // Specify how long the presigned URL lasts, in hours.
        private const double timeoutDuration = 12;
        // Specify your bucket Region (an example Region is shown).
        private static readonly RegionEndpoint bucketRegion = RegionEndpoint.USWest2;
        private static IAmazonS3 s3Client;

        public static void Main()
        {
            s3Client = new AmazonS3Client(bucketRegion);
            string urlString = GeneratePreSignedURL(timeoutDuration);
        }
        static string GeneratePreSignedURL(double duration)
        {
            string urlString = "";
            try
            {
                GetPreSignedUrlRequest request1 = new GetPreSignedUrlRequest
                {
                    BucketName = accessPointArn,
                    Key = objectKey,
                    Expires = DateTime.UtcNow.AddHours(duration)
                };
                urlString = s3Client.GetPreSignedURL(request1);
            }
            catch (AmazonS3Exception e)
            {
                Console.WriteLine("Error encountered on server. Message:'{0}' when writing an object", e.Message);
            }
            catch (Exception e)
            {
                Console.WriteLine("Unknown encountered on server. Message:'{0}' when writing an object", e.Message);
            }
            return urlString;
        }
    }
}
```

------
#### [ Python ]

The following example generates a presigned URL to share an object by using the SDK for Python \(Boto3\)\. For example, use a Boto3 client and the `generate_presigned_url` function to generate a presigned URL that allows you to `GET` an object\.

```
import boto3
    url = boto3.client('s3').generate_presigned_url(
    ClientMethod='get_object', 
    Params={'Bucket': 'ACCESS_POINT_ARN', 'Key': 'OBJECT_KEY'},
    ExpiresIn=3600)
```

For more information about using the SDK for Python \(Boto3\) to generate a presigned URL, see [Python](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html#S3.Client.generate_presigned_url) in the *AWS SDK for Python \(Boto\) API Reference*\.

------

## Using the AWS CLI<a name="S3OutpostsShareObjectPresignedCLI"></a>

The following example AWS CLI command generates a presigned URL for an S3 on Outposts bucket\. To use this example, replace the *`user input placeholders`* with your own information\.

**Note**  
When you use the AWS CLI to generate a presigned URL, the maximum expiration time for a presigned URL is 7 days from the time of creation\. 

```
aws s3 presign s3://arn:aws:s3-outposts:us-east-1:111122223333:outpost/op-01ac5d28a6a232904/accesspoint/example-outpost-access-point/mydoc.txt --expires-in 604800
```

For more information, see [presign](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3/presign.html) in the *AWS CLI Command Reference*\.