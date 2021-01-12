--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Uploading objects using presigned URLs<a name="PresignedUrlUploadObject"></a>

 You can use the AWS SDK to generate a presigned URL that you, or anyone you give the URL, can use to upload an object to Amazon S3\. When you use the URL to upload an object, Amazon S3 creates the object in the specified bucket\. If an object with the same key that is specified in the presigned URL already exists in the bucket, Amazon S3 replaces the existing object with the uploaded object\. 

 The following examples show how to upload objects using presigned URLs\. 

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
#### [ PHP ]

Please see [Upload an object using a presigned URL \(AWS SDK for PHP\)](https://docs.aws.amazon.com/sdk-for-php/v3/developer-guide/s3-presigned-url.html)\.

------