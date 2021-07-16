# Sharing an object with a presigned URL<a name="ShareObjectPreSignedURL"></a>

All objects by default are private\. Only the object owner has permission to access these objects\. However, the object owner can optionally share objects with others by creating a presigned URL, using their own security credentials, to grant time\-limited permission to download the objects\. 

When you create a presigned URL for your object, you must provide your security credentials, specify a bucket name, an object key, specify the HTTP method \(GET to download the object\) and expiration date and time\. The presigned URLs are valid only for the specified duration\. 

Anyone who receives the presigned URL can then access the object\. For example, if you have a video in your bucket and both the bucket and the object are private, you can share the video with others by generating a presigned URL\. 

**Note**  
Anyone with valid security credentials can create a presigned URL\. However, in order to successfully access an object, the presigned URL must be created by someone who has permission to perform the operation that the presigned URL is based upon\.
The credentials that you can use to create a presigned URL include:  
IAM instance profile: Valid up to 6 hours
AWS Security Token Service : Valid up to 36 hours when signed with permanent credentials, such as the credentials of the AWS account root user or an IAM user
IAM user: Valid up to 7 days when using AWS Signature Version 4  
To create a presigned URL that's valid for up to 7 days, first designate IAM user credentials \(the access key and secret access key\) to the SDK that you're using\. Then, generate a presigned URL using AWS Signature Version 4\.
If you created a presigned URL using a temporary token, then the URL expires when the token expires, even if the URL was created with a later expiration time\.
Since presigned URLs grant access to your Amazon S3 buckets to whoever has the URL, we recommend that you protect them appropriately\. For more details about protecting presigned URLs, see [Limiting presigned URL capabilities](using-presigned-url.md#PresignedUrlUploadObject-LimitCapabilities)\.

## Generating a presigned URL<a name="generating-presigned-url"></a>

You can generate a presigned URL programmatically using the [REST API](https://docs.aws.amazon.com/AmazonS3/latest/API/sigv4-query-string-auth.html#query-string-auth-v4-signing-example), the [AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/reference/s3/presign.html), and the AWS SDK for Java, \.NET, [Ruby](https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/S3/Presigner.html), [PHP](https://docs.aws.amazon.com/aws-sdk-php/v3/api/class-Aws.S3.S3Client.html#_createPresignedRequest), [Node\.js](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/S3.html#getSignedUrl-property), [Python](http://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html#S3.Client.generate_presigned_url), and [Go](https://docs.aws.amazon.com/sdk-for-go/v1/developer-guide/s3-example-presigned-urls.html)\.

### Using AWS Explorer for Visual Studio<a name="ShareObjectPreSignedURLVSExplorer"></a>

If you are using Visual Studio, you can generate a presigned URL for an object without writing any code by using AWS Explorer for Visual Studio\. Anyone with this URL can download the object\. For more information, go to [Using Amazon S3 from AWS Explorer](https://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/using-s3.html)\. 

For instructions about how to install the AWS Explorer, see [Developing with Amazon S3 using the AWS SDKs, and explorers](UsingAWSSDK.md)\.

### Using the AWS SDKs<a name="ShareObjectPreSignedURLSDK"></a>

The following examples generates a presigned URL that you can give to others so that they can retrieve an object\. For more information, see [Sharing an object with a presigned URL](#ShareObjectPreSignedURL)\. 

------
#### [ \.NET ]

**Example**  
The following example generates a presigned URL that you can give to others so that they can retrieve an object\. For more information, see [Sharing an object with a presigned URL](#ShareObjectPreSignedURL)\.   
For instructions about how to create and test a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\.  

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
#### [ Go ]

You can use SDK for Go to upload an object\. You can send a PUT request to upload data in a single operation\. For more information, see [Generate a Pre\-Signed URL for an Amazon S3 PUT Operation with a Specific Payload](https://docs.aws.amazon.com/sdk-for-go/v1/developer-guide/s3-example-presigned-urls.html) in the *AWS SDK for Go Developer Guide*\.

------
#### [ Java ]

**Example**  
The following example generates a presigned URL that you can give to others so that they can retrieve an object from an S3 bucket\. For more information, see [Sharing an object with a presigned URL](#ShareObjectPreSignedURL)\.   
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
#### [ PHP ]

For more information about using AWS SDK for PHP Version 3 to generate a presigned URL, see [Amazon S3 pre\-signed URL with AWS SDK for PHP Version 3](https://docs.aws.amazon.com/sdk-for-php/v3/developer-guide/s3-presigned-url.html) in the *AWS SDK for PHP Developer Guide*\.

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

For a complete example that shows how to generate presigned URLs and how to use the Requests package to upload and download objects, see the [ PHP presigned URL](https://docs.aws.amazon.com/code-samples/latest/catalog/python-s3-s3_basics-presigned_url.py.html) example on GitHub\. For more information about using SDK for Python \(Boto3\) to generate a presigned URL, see [Python](http://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html#S3.Client.generate_presigned_url) in the *AWS SDK for PHP API Reference*\.

------