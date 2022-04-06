# Downloading an object<a name="download-objects"></a>

This section explains how to download objects from an S3 bucket\.

Data transfer fees apply when you download objects\. For information about Amazon S3 features, and pricing, see [Amazon S3](https://aws.amazon.com/s3/)\.

You can download a single object per request using the Amazon S3 console\. To download multiple objects, use the AWS CLI, AWS SDKs, or REST API\. 

When you download an object programmatically, its metadata is returned in the response headers\. There are times when you want to override certain response header values returned in a GET response\. For example, you might override the `Content-Disposition` response header value in your GET request\. The REST GET Object API \(see [GET Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGET.html)\) allows you to specify query string parameters in your GET request to override these values\. The AWS SDKs for Java, \.NET, and PHP also provide necessary objects you can use to specify values for these response headers in your GET request\. 

When retrieving objects that are stored encrypted using server\-side encryption, you must provide appropriate request headers\. For more information, see [Protecting data using encryption](UsingEncryption.md)\.

## Using the S3 console<a name="download-objects-console"></a>

This section explains how to use the Amazon S3 console to download an object from an S3 bucket using a presigned URL\.

**Note**  
You can only download one object at a time\.
Objects with key names ending with period\(s\) "\." downloaded using the Amazon S3 console will have the period\(s\) "\." removed from the key name of the downloaded object\. To download an object with the key name ending in period\(s\) "\." retained in the downloaded object, you must use the AWS Command Line Interface \(AWS CLI\), AWS SDKs, or REST API\.

**To download an object from an S3 bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want to download an object from\.

    

1. You can download an object from an S3 bucket in any of the following ways:
   + Select the object and choose **Download** or choose **Download as** from the **Actions** menu if you want to download the object to a specific folder\.
   + If you want to download a specific version of the object, select the **Show versions** button\. Select the version of the object that you want and choose **Download** or choose **Download as** from the **Actions** menu if you want to download the object to a specific folder\.

## Using the AWS SDKs<a name="download-object-sdk"></a>

------
#### [ Java ]

When you download an object through the AWS SDK for Java, Amazon S3 returns all of the object's metadata and an input stream from which to read the object's contents\.

To retrieve an object, you do the following:
+ Execute the `AmazonS3Client.getObject()` method, providing the bucket name and object key in the request\.
+ Execute one of the `S3Object` instance methods to process the input stream\.

**Note**  
Your network connection remains open until you read all of the data or close the input stream\. We recommend that you read the content of the stream as quickly as possible\.

The following are some variations you might use:
+ Instead of reading the entire object, you can read only a portion of the object data by specifying the byte range that you want in the request\.
+ You can optionally override the response header values by using a `ResponseHeaderOverrides` object and setting the corresponding request property\. For example, you can use this feature to indicate that the object should be downloaded into a file with a different file name than the object key name\.

The following example retrieves an object from an Amazon S3 bucket three ways: first, as a complete object, then as a range of bytes from the object, then as a complete object with overridden response header values\. For more information about getting objects from Amazon S3, see [GET Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGET.html)\. For instructions on creating and testing a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\.

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.GetObjectRequest;
import com.amazonaws.services.s3.model.ResponseHeaderOverrides;
import com.amazonaws.services.s3.model.S3Object;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;

public class GetObject2 {

    public static void main(String[] args) throws IOException {
        Regions clientRegion = Regions.DEFAULT_REGION;
        String bucketName = "*** Bucket name ***";
        String key = "*** Object key ***";

        S3Object fullObject = null, objectPortion = null, headerOverrideObject = null;
        try {
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .withRegion(clientRegion)
                    .withCredentials(new ProfileCredentialsProvider())
                    .build();

            // Get an object and print its contents.
            System.out.println("Downloading an object");
            fullObject = s3Client.getObject(new GetObjectRequest(bucketName, key));
            System.out.println("Content-Type: " + fullObject.getObjectMetadata().getContentType());
            System.out.println("Content: ");
            displayTextInputStream(fullObject.getObjectContent());

            // Get a range of bytes from an object and print the bytes.
            GetObjectRequest rangeObjectRequest = new GetObjectRequest(bucketName, key)
                    .withRange(0, 9);
            objectPortion = s3Client.getObject(rangeObjectRequest);
            System.out.println("Printing bytes retrieved.");
            displayTextInputStream(objectPortion.getObjectContent());

            // Get an entire object, overriding the specified response headers, and print the object's content.
            ResponseHeaderOverrides headerOverrides = new ResponseHeaderOverrides()
                    .withCacheControl("No-cache")
                    .withContentDisposition("attachment; filename=example.txt");
            GetObjectRequest getObjectRequestHeaderOverride = new GetObjectRequest(bucketName, key)
                    .withResponseHeaders(headerOverrides);
            headerOverrideObject = s3Client.getObject(getObjectRequestHeaderOverride);
            displayTextInputStream(headerOverrideObject.getObjectContent());
        } catch (AmazonServiceException e) {
            // The call was transmitted successfully, but Amazon S3 couldn't process 
            // it, so it returned an error response.
            e.printStackTrace();
        } catch (SdkClientException e) {
            // Amazon S3 couldn't be contacted for a response, or the client
            // couldn't parse the response from Amazon S3.
            e.printStackTrace();
        } finally {
            // To ensure that the network connection doesn't remain open, close any open input streams.
            if (fullObject != null) {
                fullObject.close();
            }
            if (objectPortion != null) {
                objectPortion.close();
            }
            if (headerOverrideObject != null) {
                headerOverrideObject.close();
            }
        }
    }

    private static void displayTextInputStream(InputStream input) throws IOException {
        // Read the text input stream one line at a time and display each line.
        BufferedReader reader = new BufferedReader(new InputStreamReader(input));
        String line = null;
        while ((line = reader.readLine()) != null) {
            System.out.println(line);
        }
        System.out.println();
    }
}
```

------
#### [ \.NET ]

When you download an object, you get all of the object's metadata and a stream from which to read the contents\. You should read the content of the stream as quickly as possible because the data is streamed directly from Amazon S3 and your network connection will remain open until you read all the data or close the input stream\. You do the following to get an object:
+ Execute the `getObject` method by providing bucket name and object key in the request\.
+ Execute one of the `GetObjectResponse` methods to process the stream\.

The following are some variations you might use:
+ Instead of reading the entire object, you can read only the portion of the object data by specifying the byte range in the request, as shown in the following C\# example:  
**Example**  

  ```
  1. GetObjectRequest request = new GetObjectRequest 
  2. {
  3.     BucketName = bucketName,
  4.     Key = keyName,
  5.     ByteRange = new ByteRange(0, 10)
  6. };
  ```
+ When retrieving an object, you can optionally override the response header values \(see [Downloading an object](#download-objects)\) by using the `ResponseHeaderOverrides` object and setting the corresponding request property\. The following C\# code example shows how to do this\. For example, you can use this feature to indicate that the object should be downloaded into a file with a different file name than the object key name\.   
**Example**  

  ```
   1. GetObjectRequest request = new GetObjectRequest 
   2. {
   3.     BucketName = bucketName,
   4.     Key = keyName
   5. };
   6. 
   7. ResponseHeaderOverrides responseHeaders = new ResponseHeaderOverrides();
   8. responseHeaders.CacheControl = "No-cache";
   9. responseHeaders.ContentDisposition = "attachment; filename=testing.txt";
  10. 
  11. request.ResponseHeaderOverrides = responseHeaders;
  ```



**Example**  
The following C\# code example retrieves an object from an Amazon S3 bucket\. From the response, the example reads the object data using the `GetObjectResponse.ResponseStream` property\. The example also shows how you can use the `GetObjectResponse.Metadata` collection to read object metadata\. If the object you retrieve has the `x-amz-meta-title` metadata, the code prints the metadata value\.  
For instructions on how to create and test a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\.  

```
using Amazon;
using Amazon.S3;
using Amazon.S3.Model;
using System;
using System.IO;
using System.Threading.Tasks;

namespace Amazon.DocSamples.S3
{
    class GetObjectTest
    {
        private const string bucketName = "*** bucket name ***";
        private const string keyName = "*** object key ***";
        // Specify your bucket region (an example region is shown).
        private static readonly RegionEndpoint bucketRegion = RegionEndpoint.USWest2;
        private static IAmazonS3 client;

        public static void Main()
        {
            client = new AmazonS3Client(bucketRegion);
            ReadObjectDataAsync().Wait();
        }

        static async Task ReadObjectDataAsync()
        {
            string responseBody = "";
            try
            {
                GetObjectRequest request = new GetObjectRequest
                {
                    BucketName = bucketName,
                    Key = keyName
                };
                using (GetObjectResponse response = await client.GetObjectAsync(request))
                using (Stream responseStream = response.ResponseStream)
                using (StreamReader reader = new StreamReader(responseStream))
                {
                    string title = response.Metadata["x-amz-meta-title"]; // Assume you have "title" as medata added to the object.
                    string contentType = response.Headers["Content-Type"];
                    Console.WriteLine("Object metadata, Title: {0}", title);
                    Console.WriteLine("Content type: {0}", contentType);

                    responseBody = reader.ReadToEnd(); // Now you process the response body.
                }
            }
            catch (AmazonS3Exception e)
            {
                // If bucket or object does not exist
                Console.WriteLine("Error encountered ***. Message:'{0}' when reading object", e.Message);
            }
            catch (Exception e)
            {
                Console.WriteLine("Unknown encountered on server. Message:'{0}' when reading object", e.Message);
            }
        }
    }
}
```

------
#### [ PHP ]

This topic explains how to use a class from the AWS SDK for PHP to retrieve an Amazon S3 object\. You can retrieve an entire object or a byte range from the object\. We assume that you are already following the instructions for [Using the AWS SDK for PHP and Running PHP Examples](UsingTheMPphpAPI.md) and have the AWS SDK for PHP properly installed\. 

When retrieving an object, you can optionally override the response header values by adding the response keys, `ResponseContentType`, `ResponseContentLanguage`, `ResponseContentDisposition`, `ResponseCacheControl`, and `ResponseExpires`, to the `getObject()` method, as shown in the following PHP code example:

**Example**  

```
1. $result = $s3->getObject([
2.     'Bucket'                     => $bucket,
3.     'Key'                        => $keyname,
4.     'ResponseContentType'        => 'text/plain',
5.     'ResponseContentLanguage'    => 'en-US',
6.     'ResponseContentDisposition' => 'attachment; filename=testing.txt',
7.     'ResponseCacheControl'       => 'No-cache',
8.     'ResponseExpires'            => gmdate(DATE_RFC2822, time() + 3600),
9. ]);
```

For more information about retrieving objects, see [Downloading an object](#download-objects)\. 

The following PHP example retrieves an object and displays the content of the object in the browser\. The example shows how to use the `getObject()` method\. For information about running the PHP examples in this guide, see [Running PHP Examples](UsingTheMPphpAPI.md#running-php-samples)\. 

```
 require 'vendor/autoload.php';

use Aws\S3\S3Client;
use Aws\S3\Exception\S3Exception;

$bucket = '*** Your Bucket Name ***';
$keyname = '*** Your Object Key ***';

$s3 = new S3Client([
    'version' => 'latest',
    'region'  => 'us-east-1'
]);

try {
    // Get the object.
    $result = $s3->getObject([
        'Bucket' => $bucket,
        'Key'    => $keyname
    ]);

    // Display the object in the browser.
    header("Content-Type: {$result['ContentType']}");
    echo $result['Body'];
} catch (S3Exception $e) {
    echo $e->getMessage() . PHP_EOL;
}
```

------

## Using the REST API<a name="download-object-rest"></a>

You can use the AWS SDK to retrieve object keys from a bucket\. However, if your application requires it, you can send REST requests directly\. You can send a GET request to retrieve object keys\. 

For more information about the request and response format, see [Get Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGET.html)\.

## Using the AWS CLI<a name="download-object-cli"></a>

The example below shows you how you can use the AWS CLI to download an object from Amazon S3\. For more information and examples, see [get\-object](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/get-object.html) in the *AWS CLI Command Reference*\.

```
aws s3api get-object --bucket DOC-EXAMPLE-BUCKET1 --key dir/my_images.tar.bz2 my_images.tar.bz2
```