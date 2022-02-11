# Sharing objects using presigned URLs<a name="ShareObjectPreSignedURL"></a>

By default, all S3 objects are private\. Only the object owner has permission to access them\. However, the object owner can optionally share objects with others by creating a presigned URL, using their own security credentials, to grant time\-limited permission to download the objects\. 

When you create a presigned URL for your object, you must provide your security credentials and then specify a bucket name, an object key, an HTTP method \(GET to download the object\), and an expiration date and time\. The presigned URLs are valid only for the specified duration\. If you created a presigned URL using a temporary token, then the URL expires when the token expires, even if the URL was created with a later expiration time\. 

Anyone who receives the presigned URL can then access the object\. For example, if you have a video in your bucket and both the bucket and the object are private, you can share the video with others by generating a presigned URL\. Because presigned URLs grant access to your Amazon S3 buckets to whoever has the URL, we recommend that you protect them appropriately\. For more details about protecting presigned URLs, see [Limiting presigned URL capabilities](using-presigned-url.md#PresignedUrlUploadObject-LimitCapabilities)\.

For more information about who can create a presigned URL, see [Who can create a presigned URL](using-presigned-url.md#who-presigned-url)\.

## Generating a presigned URL to share an object<a name="generating-presigned-url"></a>

You can generate a presigned URL for an object without writing any code by using the S3 console or AWS Explorer for Visual Studio\. You can also generate a presigned URL programmatically using the AWS SDKs for Java, \.NET, [Ruby](https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/S3/Presigner.html), [PHP](https://docs.aws.amazon.com/aws-sdk-php/v3/api/class-Aws.S3.S3Client.html#_createPresignedRequest), [Node\.js](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/S3.html#getSignedUrl-property), [Python](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html#S3.Client.generate_presigned_url), and [Go](https://docs.aws.amazon.com/sdk-for-go/v1/developer-guide/s3-example-presigned-urls.html)\.

### Using the S3 console<a name="ShareObjectPreSignedURLConsole"></a>

 You can use the AWS Management Console to generate a presigned URL for an object by following these steps\. 

**To generate a presigned URL using the AWS Management Console**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that contains the object that you want a presigned URL for\.

1. In the **Objects** list, select the object that you want to create a presigned URL for\.

1. On the **Actions** menu, choose **Share with a presigned URL**\.

1. Specify how long you want the presigned URL to be valid\.

1. Choose **Create presigned URL**\.

1. When a confirmation appears, the URL is automatically copied to your clipboard\. You will see a button to copy the presigned URL if you need to copy it again\.

### Using AWS Explorer for Visual Studio<a name="ShareObjectPreSignedURLVSExplorer"></a>

If you are using Visual Studio, you can generate a presigned URL for an object without writing any code by using AWS Explorer for Visual Studio\. Anyone with this URL can download the object\. For more information, go to [Using Amazon S3 from AWS Explorer](https://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/using-s3.html)\. 

For instructions on how to install the AWS Explorer, see [Developing with Amazon S3 using the AWS SDKs, and explorers](UsingAWSSDK.md)\.

### Using the AWS SDKs<a name="ShareObjectPreSignedURLSDK"></a>

The following examples generate a presigned URL that you can give to others so that they can retrieve an object\. 

------
#### [ Java ]

**Example**  
The following example generates a presigned URL that you can give to others so that they can retrieve an object from an S3 bucket\. For more information, see [Sharing objects using presigned URLs](#ShareObjectPreSignedURL)\.   
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
        String bucketName = "*** Bucket name ***";
        String objectKey = "*** Object key ***";

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
                    new GeneratePresignedUrlRequest(bucketName, objectKey)
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
        private const string bucketName = "*** bucket name ***"; 
        private const string objectKey = "*** object key ***";
        // Specify how long the presigned URL lasts, in hours
        private const double timeoutDuration = 12;
        // Specify your bucket region (an example region is shown).
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
                    BucketName = bucketName,
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
#### [ PHP ]

For information about using AWS SDK for PHP Version 3 to generate a presigned URL, see [Amazon S3 pre\-signed URL with AWS SDK for PHP Version 3](https://docs.aws.amazon.com/sdk-for-php/v3/developer-guide/s3-presigned-url.html) in the *AWS SDK for PHP Developer Guide*\.

------
#### [ Python ]

Generate a presigned URL to share an object by using the SDK for Python \(Boto3\)\. For example, use a Boto3 client and the `generate_presigned_url` function to generate a presigned URL that GETs an object\.

```
import boto3
    url = boto3.client('s3').generate_presigned_url(
    ClientMethod='get_object', 
    Params={'Bucket': 'BUCKET_NAME', 'Key': 'OBJECT_KEY'},
    ExpiresIn=3600)
```

For more information about using SDK for Python \(Boto3\) to generate a presigned URL, see [Python](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html#S3.Client.generate_presigned_url) in the *AWS SDK for Python \(Boto\) API Reference*\.

------