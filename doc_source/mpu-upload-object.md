--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Uploading an object using multipart upload<a name="mpu-upload-object"></a>

You can use the multipart upload to programmatically upload a single object to Amazon S3\.

For more information, see the following sections\.

## Using the AWS SDKs \(high\-level API\)<a name="multipart-upload-high-level"></a>

The AWS SDK exposes a high\-level API, called `TransferManager`, that simplifies multipart uploads\. For more information, see [Uploading and copying objects using multipart upload](mpuoverview.md)\. 

You can upload data from a file or a stream\. You can also set advanced options, such as the part size you want to use for the multipart upload, or the number of concurrent threads you want to use when uploading the parts\. You can also set optional object properties, the storage class, or the access control list \(ACL\)\. You use the `PutObjectRequest` and the `TransferManagerConfiguration` classes to set these advanced options\.

When possible, `TransferManager` tries to use multiple threads to upload multiple parts of a single upload at once\. When dealing with large content sizes and high bandwidth, this can increase throughput significantly\.

In addition to file\-upload functionality, the `TransferManager` class enables you to stop an in\-progress multipart upload\. An upload is considered to be in progress after you initiate it and until you complete or stop it\. The `TransferManager` stops all in\-progress multipart uploads on a specified bucket that were initiated before a specified date and time\. 

If you need to pause and resume multipart uploads, vary part sizes during the upload, or do not know the size of the data in advance, use the low\-level PHP API\. For more information about multipart uploads, including additional functionality offered by the low\-level API methods, see [Using the AWS SDKs \(low\-level\-level API\)](#mpu-upload-low-level)\. 

------
#### [ Java ]

The following example loads an object using the high\-level multipart upload Java API \(the `TransferManager` class\)\. For instructions on creating and testing a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\. 

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.transfer.TransferManager;
import com.amazonaws.services.s3.transfer.TransferManagerBuilder;
import com.amazonaws.services.s3.transfer.Upload;

import java.io.File;

public class HighLevelMultipartUpload {

    public static void main(String[] args) throws Exception {
        Regions clientRegion = Regions.DEFAULT_REGION;
        String bucketName = "*** Bucket name ***";
        String keyName = "*** Object key ***";
        String filePath = "*** Path for file to upload ***";

        try {
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .withRegion(clientRegion)
                    .withCredentials(new ProfileCredentialsProvider())
                    .build();
            TransferManager tm = TransferManagerBuilder.standard()
                    .withS3Client(s3Client)
                    .build();

            // TransferManager processes all transfers asynchronously,
            // so this call returns immediately.
            Upload upload = tm.upload(bucketName, keyName, new File(filePath));
            System.out.println("Object upload started");

            // Optionally, wait for the upload to finish before continuing.
            upload.waitForCompletion();
            System.out.println("Object upload complete");
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

To upload a file to an S3 bucket, use the `TransferUtility` class\. When uploading data from a file, you must provide the object's key name\. If you don't, the API uses the file name for the key name\. When uploading data from a stream, you must provide the object's key name\.

To set advanced upload options—such as the part size, the number of threads when uploading the parts concurrently, metadata, the storage class, or ACL—use the `TransferUtilityUploadRequest` class\. 

The following C\# example uploads a file to an Amazon S3 bucket in multiple parts\. It shows how to use various `TransferUtility.Upload` overloads to upload a file\. Each successive call to upload replaces the previous upload\. For information about the example's compatibility with a specific version of the AWS SDK for \.NET and instructions for creating and testing a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\.

```
using Amazon;
using Amazon.S3;
using Amazon.S3.Transfer;
using System;
using System.IO;
using System.Threading.Tasks;

namespace Amazon.DocSamples.S3
{
    class UploadFileMPUHighLevelAPITest
    {
        private const string bucketName = "*** provide bucket name ***";
        private const string keyName = "*** provide a name for the uploaded object ***";
        private const string filePath = "*** provide the full path name of the file to upload ***";
        // Specify your bucket region (an example region is shown).
        private static readonly RegionEndpoint bucketRegion = RegionEndpoint.USWest2;
        private static IAmazonS3 s3Client;

        public static void Main()
        {
            s3Client = new AmazonS3Client(bucketRegion);
            UploadFileAsync().Wait();
        }

        private static async Task UploadFileAsync()
        {
            try
            {
                var fileTransferUtility =
                    new TransferUtility(s3Client);

                // Option 1. Upload a file. The file name is used as the object key name.
                await fileTransferUtility.UploadAsync(filePath, bucketName);
                Console.WriteLine("Upload 1 completed");

                // Option 2. Specify object key name explicitly.
                await fileTransferUtility.UploadAsync(filePath, bucketName, keyName);
                Console.WriteLine("Upload 2 completed");

                // Option 3. Upload data from a type of System.IO.Stream.
                using (var fileToUpload = 
                    new FileStream(filePath, FileMode.Open, FileAccess.Read))
                {
                    await fileTransferUtility.UploadAsync(fileToUpload,
                                               bucketName, keyName);
                }
                Console.WriteLine("Upload 3 completed");

                // Option 4. Specify advanced settings.
                var fileTransferUtilityRequest = new TransferUtilityUploadRequest
                {
                    BucketName = bucketName,
                    FilePath = filePath,
                    StorageClass = S3StorageClass.StandardInfrequentAccess,
                    PartSize = 6291456, // 6 MB.
                    Key = keyName,
                    CannedACL = S3CannedACL.PublicRead
                };
                fileTransferUtilityRequest.Metadata.Add("param1", "Value1");
                fileTransferUtilityRequest.Metadata.Add("param2", "Value2");

                await fileTransferUtility.UploadAsync(fileTransferUtilityRequest);
                Console.WriteLine("Upload 4 completed");
            }
            catch (AmazonS3Exception e)
            {
                Console.WriteLine("Error encountered on server. Message:'{0}' when writing an object", e.Message);
            }
            catch (Exception e)
            {
                Console.WriteLine("Unknown encountered on server. Message:'{0}' when writing an object", e.Message);
            }

        }
    }
}
```

------
#### [ PHP ]

This topic explains how to use the high\-level `Aws\S3\Model\MultipartUpload\UploadBuilder` class from the AWS SDK for PHP for multipart file uploads\. It assumes that you are already following the instructions for [Using the AWS SDK for PHP and Running PHP Examples](UsingTheMPphpAPI.md) and have the AWS SDK for PHP properly installed\.

The following PHP example uploads a file to an Amazon S3 bucket\. The example demonstrates how to set parameters for the `MultipartUploader` object\. 

For information about running the PHP examples in this guide, see [Running PHP Examples](UsingTheMPphpAPI.md#running-php-samples)\.

```
 require 'vendor/autoload.php';

use Aws\Common\Exception\MultipartUploadException;
use Aws\S3\MultipartUploader;
use Aws\S3\S3Client;

$bucket = '*** Your Bucket Name ***';
$keyname = '*** Your Object Key ***';
                        
$s3 = new S3Client([
    'version' => 'latest',
    'region'  => 'us-east-1'
]);
 
// Prepare the upload parameters.
$uploader = new MultipartUploader($s3, '/path/to/large/file.zip', [
    'bucket' => $bucket,
    'key'    => $keyname
]);

// Perform the upload.
try {
    $result = $uploader->upload();
    echo "Upload complete: {$result['ObjectURL']}" . PHP_EOL;
} catch (MultipartUploadException $e) {
    echo $e->getMessage() . PHP_EOL;
}
```

------

## Using the AWS SDKs \(low\-level\-level API\)<a name="mpu-upload-low-level"></a>

The AWS SDK exposes a low\-level API that closely resembles the Amazon S3 REST API for multipart uploads \(see [Uploading and copying objects using multipart upload](mpuoverview.md)\. Use the low\-level API when you need to pause and resume multipart uploads, vary part sizes during the upload, or do not know the size of the upload data in advance\. When you don't have these requirements, use the high\-level API \(see [Using the AWS SDKs \(high\-level API\)](#multipart-upload-high-level)\)\.

------
#### [ Java ]

The following example shows how to use the low\-level Java classes to upload a file\. It performs the following steps:
+ Initiates a multipart upload using the `AmazonS3Client.initiateMultipartUpload()` method, and passes in an `InitiateMultipartUploadRequest` object\.
+ Saves the upload ID that the `AmazonS3Client.initiateMultipartUpload()` method returns\. You provide this upload ID for each subsequent multipart upload operation\.
+ Uploads the parts of the object\. For each part, you call the `AmazonS3Client.uploadPart()` method\. You provide part upload information using an `UploadPartRequest` object\. 
+ For each part, saves the ETag from the response of the `AmazonS3Client.uploadPart()` method in a list\. You use the ETag values to complete the multipart upload\.
+ Calls the `AmazonS3Client.completeMultipartUpload()` method to complete the multipart upload\. 

**Example**  
For instructions on creating and testing a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\.   

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.*;

import java.io.File;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

public class LowLevelMultipartUpload {

    public static void main(String[] args) throws IOException {
        Regions clientRegion = Regions.DEFAULT_REGION;
        String bucketName = "*** Bucket name ***";
        String keyName = "*** Key name ***";
        String filePath = "*** Path to file to upload ***";

        File file = new File(filePath);
        long contentLength = file.length();
        long partSize = 5 * 1024 * 1024; // Set part size to 5 MB. 

        try {
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .withRegion(clientRegion)
                    .withCredentials(new ProfileCredentialsProvider())
                    .build();

            // Create a list of ETag objects. You retrieve ETags for each object part uploaded,
            // then, after each individual part has been uploaded, pass the list of ETags to 
            // the request to complete the upload.
            List<PartETag> partETags = new ArrayList<PartETag>();

            // Initiate the multipart upload.
            InitiateMultipartUploadRequest initRequest = new InitiateMultipartUploadRequest(bucketName, keyName);
            InitiateMultipartUploadResult initResponse = s3Client.initiateMultipartUpload(initRequest);

            // Upload the file parts.
            long filePosition = 0;
            for (int i = 1; filePosition < contentLength; i++) {
                // Because the last part could be less than 5 MB, adjust the part size as needed.
                partSize = Math.min(partSize, (contentLength - filePosition));

                // Create the request to upload a part.
                UploadPartRequest uploadRequest = new UploadPartRequest()
                        .withBucketName(bucketName)
                        .withKey(keyName)
                        .withUploadId(initResponse.getUploadId())
                        .withPartNumber(i)
                        .withFileOffset(filePosition)
                        .withFile(file)
                        .withPartSize(partSize);

                // Upload the part and add the response's ETag to our list.
                UploadPartResult uploadResult = s3Client.uploadPart(uploadRequest);
                partETags.add(uploadResult.getPartETag());

                filePosition += partSize;
            }

            // Complete the multipart upload.
            CompleteMultipartUploadRequest compRequest = new CompleteMultipartUploadRequest(bucketName, keyName,
                    initResponse.getUploadId(), partETags);
            s3Client.completeMultipartUpload(compRequest);
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

The following C\# example shows how to use the low\-level AWS SDK for \.NET multipart upload API to upload a file to an S3 bucket\. For information about Amazon S3 multipart uploads, see [Uploading and copying objects using multipart upload](mpuoverview.md)\.

**Note**  
When you use the AWS SDK for \.NET API to upload large objects, a timeout might occur while data is being written to the request stream\. You can set an explicit timeout using the `UploadPartRequest`\. 

The following C\# example uploads a file to an S3 bucket using the low\-level multipart upload API\. For information about the example's compatibility with a specific version of the AWS SDK for \.NET and instructions for creating and testing a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\.

```
using Amazon;
using Amazon.Runtime;
using Amazon.S3;
using Amazon.S3.Model;
using System;
using System.Collections.Generic;
using System.IO;
using System.Threading.Tasks;

namespace Amazon.DocSamples.S3
{
    class UploadFileMPULowLevelAPITest
    {
        private const string bucketName = "*** provide bucket name ***";
        private const string keyName = "*** provide a name for the uploaded object ***";
        private const string filePath = "*** provide the full path name of the file to upload ***";
        // Specify your bucket region (an example region is shown).
        private static readonly RegionEndpoint bucketRegion = RegionEndpoint.USWest2;
        private static IAmazonS3 s3Client;

        public static void Main()
        {
            s3Client = new AmazonS3Client(bucketRegion);
            Console.WriteLine("Uploading an object");
            UploadObjectAsync().Wait(); 
        }

        private static async Task UploadObjectAsync()
        {
            // Create list to store upload part responses.
            List<UploadPartResponse> uploadResponses = new List<UploadPartResponse>();

            // Setup information required to initiate the multipart upload.
            InitiateMultipartUploadRequest initiateRequest = new InitiateMultipartUploadRequest
            {
                BucketName = bucketName,
                Key = keyName
            };

            // Initiate the upload.
            InitiateMultipartUploadResponse initResponse =
                await s3Client.InitiateMultipartUploadAsync(initiateRequest);

            // Upload parts.
            long contentLength = new FileInfo(filePath).Length;
            long partSize = 5 * (long)Math.Pow(2, 20); // 5 MB

            try
            {
                Console.WriteLine("Uploading parts");
        
                long filePosition = 0;
                for (int i = 1; filePosition < contentLength; i++)
                {
                    UploadPartRequest uploadRequest = new UploadPartRequest
                        {
                            BucketName = bucketName,
                            Key = keyName,
                            UploadId = initResponse.UploadId,
                            PartNumber = i,
                            PartSize = partSize,
                            FilePosition = filePosition,
                            FilePath = filePath
                        };

                    // Track upload progress.
                    uploadRequest.StreamTransferProgress +=
                        new EventHandler<StreamTransferProgressArgs>(UploadPartProgressEventCallback);

                    // Upload a part and add the response to our list.
                    uploadResponses.Add(await s3Client.UploadPartAsync(uploadRequest));

                    filePosition += partSize;
                }

                // Setup to complete the upload.
                CompleteMultipartUploadRequest completeRequest = new CompleteMultipartUploadRequest
                    {
                        BucketName = bucketName,
                        Key = keyName,
                        UploadId = initResponse.UploadId
                     };
                completeRequest.AddPartETags(uploadResponses);

                // Complete the upload.
                CompleteMultipartUploadResponse completeUploadResponse =
                    await s3Client.CompleteMultipartUploadAsync(completeRequest);
            }
            catch (Exception exception)
            {
                Console.WriteLine("An AmazonS3Exception was thrown: { 0}", exception.Message);

                // Abort the upload.
                AbortMultipartUploadRequest abortMPURequest = new AbortMultipartUploadRequest
                {
                    BucketName = bucketName,
                    Key = keyName,
                    UploadId = initResponse.UploadId
                };
               await s3Client.AbortMultipartUploadAsync(abortMPURequest);
            }
        }
        public static void UploadPartProgressEventCallback(object sender, StreamTransferProgressArgs e)
        {
            // Process event. 
            Console.WriteLine("{0}/{1}", e.TransferredBytes, e.TotalBytes);
        }
    }
}
```

------
#### [ PHP ]

This topic shows how to use the low\-level `uploadPart` method from version 3 of the AWS SDK for PHP to upload a file in multiple parts\. It assumes that you are already following the instructions for [Using the AWS SDK for PHP and Running PHP Examples](UsingTheMPphpAPI.md) and have the AWS SDK for PHP properly installed\.

The following PHP example uploads a file to an Amazon S3 bucket using the low\-level PHP API multipart upload\. For information about running the PHP examples in this guide, see [Running PHP Examples](UsingTheMPphpAPI.md#running-php-samples)\. 

```
 require 'vendor/autoload.php';

use Aws\S3\S3Client;

$bucket = '*** Your Bucket Name ***';
$keyname = '*** Your Object Key ***';
$filename = '*** Path to and Name of the File to Upload ***';

$s3 = new S3Client([
    'version' => 'latest',
    'region'  => 'us-east-1'
]);

$result = $s3->createMultipartUpload([
    'Bucket'       => $bucket,
    'Key'          => $keyname,
    'StorageClass' => 'REDUCED_REDUNDANCY',
    'Metadata'     => [
        'param1' => 'value 1',
        'param2' => 'value 2',
        'param3' => 'value 3'
    ]
]);
$uploadId = $result['UploadId'];

// Upload the file in parts.
try {
    $file = fopen($filename, 'r');
    $partNumber = 1;
    while (!feof($file)) {
        $result = $s3->uploadPart([
            'Bucket'     => $bucket,
            'Key'        => $keyname,
            'UploadId'   => $uploadId,
            'PartNumber' => $partNumber,
            'Body'       => fread($file, 5 * 1024 * 1024),
        ]);
        $parts['Parts'][$partNumber] = [
            'PartNumber' => $partNumber,
            'ETag' => $result['ETag'],
        ];
        $partNumber++;

        echo "Uploading part {$partNumber} of {$filename}." . PHP_EOL;
    }
    fclose($file);
} catch (S3Exception $e) {
    $result = $s3->abortMultipartUpload([
        'Bucket'   => $bucket,
        'Key'      => $keyname,
        'UploadId' => $uploadId
    ]);

    echo "Upload of {$filename} failed." . PHP_EOL;
}

// Complete the multipart upload.
$result = $s3->completeMultipartUpload([
    'Bucket'   => $bucket,
    'Key'      => $keyname,
    'UploadId' => $uploadId,
    'MultipartUpload'    => $parts,
]);
$url = $result['Location'];

echo "Uploaded {$filename} to {$url}." . PHP_EOL;
```

------

## Using the AWS SDK for Ruby<a name="mpuoverview-ruby-sdk"></a>

The AWS SDK for Ruby version 3 supports Amazon S3 multipart uploads in two ways\. For the first option, you can use managed file uploads\. For more information, see [Uploading Files to Amazon S3](https://aws.amazon.com/blogs/developer/uploading-files-to-amazon-s3/) in the *AWS Developer Blog*\. Managed file uploads are the recommended method for uploading files to a bucket\. They provide the following benefits:
+ Manage multipart uploads for objects larger than 15MB\.
+ Correctly open files in binary mode to avoid encoding issues\.
+ Use multiple threads for uploading parts of large objects in parallel\.

Alternatively, you can use the following multipart upload client operations directly:
+ [create\_multipart\_upload](https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/S3/Client.html#create_multipart_upload-instance_method) – Initiates a multipart upload and returns an upload ID\.
+ [upload\_part](https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/S3/Client.html#upload_part-instance_method) – Uploads a part in a multipart upload\.
+ [upload\_part\_copy](https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/S3/Client.html#upload_part_copy-instance_method) – Uploads a part by copying data from an existing object as data source\.
+ [complete\_multipart\_upload](https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/S3/Client.html#complete_multipart_upload-instance_method) – Completes a multipart upload by assembling previously uploaded parts\.
+ [abort\_multipart\_upload](https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/S3/Client.html#abort_multipart_upload-instance_method) – Stops a multipart upload\.

For more information, see [Using the AWS SDK for Ruby \- Version 3](UsingTheMPRubyAPI.md)\.

## Using the REST API<a name="UsingRESTAPImpUpload"></a>

The following sections in the *Amazon Simple Storage Service API Reference* describe the REST API for multipart upload\. 
+ [Initiate Multipart Upload](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadInitiate.html)
+ [Upload Part](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadUploadPart.html)
+ [Complete Multipart Upload](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadComplete.html)
+ [Stop Multipart Upload](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadAbort.html)
+ [List Parts](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadListParts.html)
+ [List Multipart Uploads](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadListMPUpload.html)

## Using the AWS CLI<a name="UsingCLImpUpload"></a>

The following sections in the AWS Command Line Interface \(AWS CLI\) describe the operations for multipart upload\. 
+ [Initiate Multipart Upload](https://docs.aws.amazon.com/cli/latest/reference/s3api/create-multipart-upload.html)
+ [Upload Part](https://docs.aws.amazon.com/cli/latest/reference/s3api/upload-part.html)
+ [Upload Part \(Copy\)](https://docs.aws.amazon.com/cli/latest/reference/s3api/upload-part-copy.html)
+ [Complete Multipart Upload](https://docs.aws.amazon.com/cli/latest/reference/s3api/complete-multipart-upload.html)
+ [Abort Multipart Upload](https://docs.aws.amazon.com/cli/latest/reference/s3api/abort-multipart-upload.html)
+ [List Parts](https://docs.aws.amazon.com/cli/latest/reference/s3api/list-parts.html)
+ [List Multipart Uploads](https://docs.aws.amazon.com/cli/latest/reference/s3api/list-multipart-uploads.html)

You can also use the REST API to make your own REST requests, or you can use one of the AWS SDKs\. For more information about the REST API, see [Using the REST API](#UsingRESTAPImpUpload)\. For more information about the SDKs, see [Uploading an object using multipart upload](#mpu-upload-object)\.