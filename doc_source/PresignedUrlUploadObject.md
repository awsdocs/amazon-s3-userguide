# Uploading objects using presigned URLs<a name="PresignedUrlUploadObject"></a>

A presigned URL gives you access to the object identified in the URL, provided that the creator of the presigned URL has permissions to access that object\. That is, if you receive a presigned URL to upload an object, you can upload the object only if the creator of the presigned URL has the necessary permissions to upload that object\. 

All objects and buckets by default are private\. The presigned URLs are useful if you want your user/customer to be able to upload a specific object to your bucket, but you don't require them to have AWS security credentials or permissions\. 

When you create a presigned URL, you must provide your security credentials and then specify a bucket name, an object key, an HTTP method \(PUT for uploading objects\), and an expiration date and time\. The presigned URLs are valid only for the specified duration\. That is, you must start the action before the expiration date and time\. If the action consists of multiple steps, such as a multipart upload, all steps must be started before the expiration, otherwise you will receive an error when Amazon S3 attempts to start a step with an expired URL\.

You can use the presigned URL multiple times, up to the expiration date and time\.

**Presigned URL access**  
Since presigned URLs grant access to your Amazon S3 buckets to whoever has the URL, we recommend that you protect them appropriately\. For more details about protecting presigned URLs, see [Limiting presigned URL capabilities](using-presigned-url.md#PresignedUrlUploadObject-LimitCapabilities)\.

Anyone with valid security credentials can create a presigned URL\. However, for you to successfully upload an object, the presigned URL must be created by someone who has permission to perform the operation that the presigned URL is based upon\.

**Generate a presigned URL for object upload**  
You can generate a presigned URL programmatically using the [REST API](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOST.html), \.NET, AWS SDK for Java, Ruby, [AWS SDK for JavaScript](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/S3.html#getSignedUrl-property), PHP, , and [Python](http://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html#S3.Client.generate_presigned_url)\.

If you are using Microsoft Visual Studio, you can also use AWS Explorer to generate a presigned object URL without writing any code\. Anyone who receives a valid presigned URL can then programmatically upload an object\. For more information, see [Using Amazon S3 from AWS Explorer](https://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/using-s3.html)\. For instructions on how to install AWS Explorer, see [Developing with Amazon S3 using the AWS SDKs, and explorers](UsingAWSSDK.md)\.

You can use the AWS SDK to generate a presigned URL that you, or anyone you give the URL, can use to upload an object to Amazon S3\. When you use the URL to upload an object, Amazon S3 creates the object in the specified bucket\. If an object with the same key that is specified in the presigned URL already exists in the bucket, Amazon S3 replaces the existing object with the uploaded object\.

## Examples<a name="presigned-urls-upload-examples"></a>

The following examples show how to upload objects using presigned URLs\.

------
#### [ \.NET ]

The following C\# example shows how to use the AWS SDK for \.NET to upload an object to an S3 bucket using a presigned URL\.

This example generates a presigned URL for a specific object and uses it to upload a file\. For information about the example's compatibility with a specific version of the AWS SDK for \.NET and instructions about how to create and test a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\.

```
using Amazon;
using Amazon.S3;
using Amazon.S3.Model;
using System;
using System.IO;
using System.Net;

namespace Amazon.DocSamples.S3
{
    class UploadObjectUsingPresignedURLTest
    {
        private const string bucketName = "*** provide bucket name ***";
        private const string objectKey  = "*** provide the name for the uploaded object ***";
        private const string filePath   = "*** provide the full path name of the file to upload ***";
        // Specify how long the presigned URL lasts, in hours
        private const double timeoutDuration = 12;
        // Specify your bucket region (an example region is shown).
        private static readonly RegionEndpoint bucketRegion = RegionEndpoint.USWest2; 
        private static IAmazonS3 s3Client;

        public static void Main()
        {
            s3Client = new AmazonS3Client(bucketRegion);
            var url = GeneratePreSignedURL(timeoutDuration);
            UploadObject(url);
        }

        private static void UploadObject(string url)
        {
            HttpWebRequest httpRequest = WebRequest.Create(url) as HttpWebRequest;
            httpRequest.Method = "PUT";
            using (Stream dataStream = httpRequest.GetRequestStream())
            {
                var buffer = new byte[8000];
                using (FileStream fileStream = new FileStream(filePath, FileMode.Open, FileAccess.Read))
                {
                    int bytesRead = 0;
                    while ((bytesRead = fileStream.Read(buffer, 0, buffer.Length)) > 0)
                    {
                        dataStream.Write(buffer, 0, bytesRead);
                    }
                }
            }
            HttpWebResponse response = httpRequest.GetResponse() as HttpWebResponse;
        }

        private static string GeneratePreSignedURL(double duration)
        {
            var request = new GetPreSignedUrlRequest
            {
                BucketName = bucketName,
                Key        = objectKey,
                Verb       = HttpVerb.PUT,
                Expires    = DateTime.UtcNow.AddHours(duration)
            };

           string url = s3Client.GetPreSignedURL(request);
           return url;
        }
    }
}
```

------
#### [ Java ]

To successfully complete an upload, you must do the following:
+ Specify the HTTP PUT verb when creating the `GeneratePresignedUrlRequest` and `HttpURLConnection` objects\.
+ Interact with the `HttpURLConnection` object in some way after finishing the upload\. The following example accomplishes this by using the `HttpURLConnection` object to check the HTTP response code\.

**Example**  
This example generates a presigned URL and uses it to upload sample data as an object\. For instructions on creating and testing a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\.  

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.HttpMethod;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.GeneratePresignedUrlRequest;
import com.amazonaws.services.s3.model.S3Object;

import java.io.IOException;
import java.io.OutputStreamWriter;
import java.net.HttpURLConnection;
import java.net.URL;

public class GeneratePresignedUrlAndUploadObject {

    public static void main(String[] args) throws IOException {
        Regions clientRegion = Regions.DEFAULT_REGION;
        String bucketName = "*** Bucket name ***";
        String objectKey = "*** Object key ***";

        try {
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .withCredentials(new ProfileCredentialsProvider())
                    .withRegion(clientRegion)
                    .build();

            // Set the pre-signed URL to expire after one hour.
            java.util.Date expiration = new java.util.Date();
            long expTimeMillis = expiration.getTime();
            expTimeMillis += 1000 * 60 * 60;
            expiration.setTime(expTimeMillis);

            // Generate the pre-signed URL.
            System.out.println("Generating pre-signed URL.");
            GeneratePresignedUrlRequest generatePresignedUrlRequest = new GeneratePresignedUrlRequest(bucketName, objectKey)
                    .withMethod(HttpMethod.PUT)
                    .withExpiration(expiration);
            URL url = s3Client.generatePresignedUrl(generatePresignedUrlRequest);

            // Create the connection and use it to upload the new object using the pre-signed URL.
            HttpURLConnection connection = (HttpURLConnection) url.openConnection();
            connection.setDoOutput(true);
            connection.setRequestMethod("PUT");
            OutputStreamWriter out = new OutputStreamWriter(connection.getOutputStream());
            out.write("This text uploaded as an object via presigned URL.");
            out.close();

            // Check the HTTP response code. To complete the upload and make the object available, 
            // you must interact with the connection object in some way.
            connection.getResponseCode();
            System.out.println("HTTP response code: " + connection.getResponseCode());

            // Check to make sure that the object was uploaded successfully.
            S3Object object = s3Client.getObject(bucketName, objectKey);
            System.out.println("Object " + object.getKey() + " created in bucket " + object.getBucketName());
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
#### [ JavaScript ]

**Example**  
For an AWS SDK for JavaScript example on using the presigned URL to upload objects, see [ Create a presigned URL to upload objects to an Amazon S3 bucket](https://docs.aws.amazon.com/sdk-for-javascript/v3/developer-guide/s3-example-creating-buckets.html#s3-create-presigendurl-put)\.

**Example**  
The following AWS SDK for JavaScript example uses a presigned URL to delete an object:  

```
          // Import the required AWS SDK clients and commands for Node.js
import {
  CreateBucketCommand,
  DeleteObjectCommand,
  PutObjectCommand,
  DeleteBucketCommand }
from "@aws-sdk/client-s3";
import { s3Client } from "./libs/s3Client.js"; // Helper function that creates Amazon S3 service client module.
import { getSignedUrl } from "@aws-sdk/s3-request-presigner";
import fetch from "node-fetch";

// Set parameters
// Create a random names for the Amazon Simple Storage Service (Amazon S3) bucket and key
const bucketParams = {
  Bucket: `test-bucket-${Math.ceil(Math.random() * 10 ** 10)}`,
  Key: `test-object-${Math.ceil(Math.random() * 10 ** 10)}`,
  Body: "BODY"
};
const run = async () => {
  try {
    // Create an Amazon S3 bucket.
    console.log(`Creating bucket ${bucketParams.Bucket}`);
    await s3Client.send(new CreateBucketCommand({ Bucket: bucketParams.Bucket }));
    console.log(`Waiting for "${bucketParams.Bucket}" bucket creation...`);
  } catch (err) {
    console.log("Error creating bucket", err);
  }
  try {
    // Create the command.
    const command = new PutObjectCommand(bucketParams);

    // Create the presigned URL.
    const signedUrl = await getSignedUrl(s3Client, command, {
      expiresIn: 3600,
    });
    console.log(
      `\nPutting "${bucketParams.Key}" using signedUrl with body "${bucketParams.Body}" in v3`
    );
    console.log(signedUrl);
    const response = await fetch(signedUrl);
    console.log(
      `\nResponse returned by signed URL: ${await response.text()}\n`
    );
    return response;
  } catch (err) {
    console.log("Error creating presigned URL", err);
  }
  try {
    // Delete the object.
    console.log(`\nDeleting object "${bucketParams.Key}"} from bucket`);
    await s3Client.send(
      new DeleteObjectCommand({ Bucket: bucketParams.Bucket, Key: bucketParams.Key })
    );
  } catch (err) {
    console.log("Error deleting object", err);
  }
  try {
    // Delete the Amazon S3 bucket.
    console.log(`\nDeleting bucket ${bucketParams.Bucket}`);
    await s3.send(new DeleteBucketCommand({ Bucket: bucketParams.Bucket }));
  } catch (err) {
    console.log("Error deleting bucket", err);
  }
};
run();
```

------
#### [ Python ]

Generate a presigned URL to upload an object by using the SDK for Python \(Boto3\)\. For example, use a Boto3 client and the `generate_presigned_url` function to generate a presigned URL that PUTs an object\.

```
import boto3
    url = boto3.client('s3').generate_presigned_url(
    ClientMethod='put_object', 
    Params={'Bucket': 'BUCKET_NAME', 'Key': 'OBJECT_KEY'},
    ExpiresIn=3600)
```

For a complete example that shows how to generate presigned URLs and how to use the Requests package to upload and download objects, see the [ Python presigned URL](https://docs.aws.amazon.com/code-samples/latest/catalog/python-s3-s3_basics-presigned_url.py.html) example on GitHub\. For more information about using SDK for Python \(Boto3\) to generate a presigned URL, see [Python](http://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html#S3.Client.generate_presigned_url) in the *AWS SDK for Python \(Boto\) API Reference*\.

------
#### [ Ruby ]

The following tasks guide you through using a Ruby script to upload an object using a presigned URL for SDK for Ruby \- Version 3\.


**Uploading objects \- SDK for Ruby \- version 3**  

|  |  | 
| --- |--- |
|  1  |  Create an instance of the `Aws::S3::Resource` class\.  | 
|  2  |  Provide a bucket name and an object key by calling the `#bucket[]` and the `#object[]` methods of your `Aws::S3::Resource` class instance\. Generate a presigned URL by creating an instance of the `URI` class, and use it to parse the `.presigned_url` method of your `Aws::S3::Resource` class instance\. You must specify `:put` as an argument to `.presigned_url`, and you must specify `PUT` to `Net::HTTP::Session#send_request` if you want to upload an object\.  | 
|  3  |  Anyone with the presigned URL can upload an object\.  The upload creates an object or replaces any existing object with the same key that is specified in the presigned URL\.  | 

The following Ruby code example demonstrates the preceding tasks for SDK for Ruby \- Version 3\.

**Example**  

```
require 'aws-sdk-s3'
require 'net/http'

# Uploads an object to a bucket in Amazon Simple Storage Service (Amazon S3)
#   by using a presigned URL.
#
# Prerequisites:
#
# - An S3 bucket.
# - An object in the bucket to upload content to.
#
# @param s3_client [Aws::S3::Resource] An initialized S3 resource.
# @param bucket_name [String] The name of the bucket.
# @param object_key [String] The name of the object.
# @param object_content [String] The content to upload to the object.
# @param http_client [Net::HTTP] An initialized HTTP client.
#   This is especially useful for testing with mock HTTP clients.
#   If not specified, a default HTTP client is created.
# @return [Boolean] true if the object was uploaded; otherwise, false.
# @example
#   exit 1 unless object_uploaded_to_presigned_url?(
#     Aws::S3::Resource.new(region: 'us-east-1'),
#     'doc-example-bucket',
#     'my-file.txt',
#     'This is the content of my-file.txt'
#   )
def object_uploaded_to_presigned_url?(
  s3_resource,
  bucket_name,
  object_key,
  object_content,
  http_client = nil
)
  object = s3_resource.bucket(bucket_name).object(object_key)
  url = URI.parse(object.presigned_url(:put))

  if http_client.nil?
    Net::HTTP.start(url.host) do |http|
      http.send_request(
        'PUT',
        url.request_uri,
        object_content,
        'content-type' => ''
      )
    end
  else
    http_client.start(url.host) do |http|
      http.send_request(
        'PUT',
        url.request_uri,
        object_content,
        'content-type' => ''
      )
    end
  end
  content = object.get.body
  puts "The presigned URL for the object '#{object_key}' in the bucket " \
    "'#{bucket_name}' is:\n\n"
  puts url
  puts "\nUsing this presigned URL to get the content that " \
    "was just uploaded to this object, the object\'s content is:\n\n"
  puts content.read
  return true
rescue StandardError => e
  puts "Error uploading to presigned URL: #{e.message}"
  return false
end
```

------