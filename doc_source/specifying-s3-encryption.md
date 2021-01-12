--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Specifying Amazon S3 encryption<a name="specifying-s3-encryption"></a>

 When you create an object, you can specify the use of server\-side encryption with Amazon S3\-managed encryption keys to encrypt your data\. This is true when you are either uploading a new object or copying an existing object\. This encryption is known as SSE\-S3\. 

 You can specify SSE\-S3 using the S3 console, REST APIs, AWS SDKs, and AWS CLI\. For more information, see the topics below\. 

## Using the S3 console<a name="add-object-encryption-s3"></a>

 This topic describes how to set or change the type of encryption an object using the AWS Management Console\. When you copy and object using the console, it copies the object as is\. That manes if the source is encrypted, the target object is also encrypted\. The console also allows you to add or change encryption for an object\. 

**Note**  
If you change an object's encryption, a new object is created to replace the old one\. If S3 Versioning is enabled, a new version of the object is created, and the existing object becomes an older version\. The role that changes the property also becomes the owner of the new object or \(object version\)\. 

**To add or change encryption for an object**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that contains the object\.

1. In the **Objects** list, choose the name of the object that you want to add or change encryption for\.

   The **Object overview** opens, displaying the properties for your object\.

1. Under **Server\-side encryption settings**, choose **Edit**\.

   The **Edit server\-side encryption** page opens\.

1. To enable server\-side encryption for your object, under **Server\-side encryption**, choose **Enable**\.

1. To enable server\-side encryption using an Amazon S3\-managed key, under **Encryption key type**, choose **Amazon S3 key \(SSE\-S3\)**\.

   For more information about using Amazon S3 server\-side encryption to encrypt your data, see [On the **Set Properties** page, choose the storage class and encryption method to use for the files that you are uploading\. You can also add or modify metadata\. Protecting data using server\-side encryption with Amazon S3\-managed encryption keys \(SSE\-S3\)](UsingServerSideEncryption.md)\.

1. Choose **Save changes**\.

**Note**  
This action applies encryption to all specified objects\. When encrypting folders, wait for the save operation to finish before adding new objects to the folder\.

## Using the REST API<a name="SSEUsingRESTAPI"></a>

At the time of object creation—that is, when you are uploading a new object or making a copy of an existing object—you can specify if you want Amazon S3 to encrypt your data by adding the `x-amz-server-side-encryption` header to the request\. Set the value of the header to the encryption algorithm `AES256` that Amazon S3 supports\. Amazon S3 confirms that your object is stored using server\-side encryption by returning the response header `x-amz-server-side-encryption`\. 

The following REST upload APIs accept the `x-amz-server-side-encryption` request header\.
+ [PUT Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPUT.html)
+ [PUT Object \- Copy](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectCOPY.html)
+ [POST Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOST.html)
+ [Initiate Multipart Upload](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadInitiate.html)

When uploading large objects using the multipart upload API, you can specify server\-side encryption by adding the `x-amz-server-side-encryption` header to the Initiate Multipart Upload request\. When you are copying an existing object, regardless of whether the source object is encrypted or not, the destination object is not encrypted unless you explicitly request server\-side encryption\.

The response headers of the following REST APIs return the `x-amz-server-side-encryption` header when an object is stored using server\-side encryption\. 
+ [PUT Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPUT.html)
+ [PUT Object \- Copy](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectCOPY.html)
+ [POST Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOST.html)
+ [Initiate Multipart Upload](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadInitiate.html)
+ [Upload Part](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadUploadPart.html)
+ [Upload Part \- Copy](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadUploadPartCopy.html)
+ [Complete Multipart Upload](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadComplete.html)
+ [Get Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGET.html)
+ [Head Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectHEAD.html)

**Note**  
Encryption request headers should not be sent for `GET` requests and `HEAD` requests if your object uses SSE\-S3 or you’ll get an HTTP 400 BadRequest error\.

## Using the AWS SDKs<a name="s3-using-sdks"></a>

 When using AWS SDKs, you can request Amazon S3 to use Amazon S3\-managed encryption keys\. This section provides examples of using the AWS SDKs in multiple languages\. For information about other SDKs, go to [Sample Code and Libraries](https://aws.amazon.com/code)\. 

------
#### [ Java ]

When you use the AWS SDK for Java to upload an object, you can use server\-side encryption to encrypt it\. To request server\-side encryption, use the `ObjectMetadata` property of the `PutObjectRequest` to set the `x-amz-server-side-encryption` request header\. When you call the `putObject()` method of the `AmazonS3Client`, Amazon S3 encrypts and saves the data\.

You can also request server\-side encryption when uploading objects with the multipart upload API: 
+ When using the high\-level multipart upload API, you use the `TransferManager` methods to apply server\-side encryption to objects as you upload them\. You can use any of the upload methods that take `ObjectMetadata` as a parameter\. For more information, see [Uploading an object using multipart upload](mpu-upload-object.md)\.
+ When using the low\-level multipart upload API, you specify server\-side encryption when you initiate the multipart upload\. You add the `ObjectMetadata` property by calling the `InitiateMultipartUploadRequest.setObjectMetadata()` method\. For more information, see [Using the AWS SDKs \(low\-level\-level API\)](mpu-upload-object.md#mpu-upload-low-level)\.

You can't directly change the encryption state of an object \(encrypting an unencrypted object or decrypting an encrypted object\)\. To change an object's encryption state, you make a copy of the object, specifying the desired encryption state for the copy, and then delete the original object\. Amazon S3 encrypts the copied object only if you explicitly request server\-side encryption\. To request encryption of the copied object through the Java API, use the `ObjectMetadata` property to specify server\-side encryption in the `CopyObjectRequest`\.

**Example**  
The following example shows how to set server\-side encryption using the AWS SDK for Java\. It shows how to perform the following tasks:  
+ Upload a new object using server\-side encryption\.
+ Change an object's encryption state \(in this example, encrypting a previously unencrypted object\) by making a copy of the object\.
+ Check the encryption state of the object\.
For more information about server\-side encryption, see [Using the REST API](#SSEUsingRESTAPI)\. For instructions on creating and testing a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\.   

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.internal.SSEResultBase;
import com.amazonaws.services.s3.model.*;

import java.io.ByteArrayInputStream;

public class SpecifyServerSideEncryption {

    public static void main(String[] args) {
        Regions clientRegion = Regions.DEFAULT_REGION;
        String bucketName = "*** Bucket name ***";
        String keyNameToEncrypt = "*** Key name for an object to upload and encrypt ***";
        String keyNameToCopyAndEncrypt = "*** Key name for an unencrypted object to be encrypted by copying ***";
        String copiedObjectKeyName = "*** Key name for the encrypted copy of the unencrypted object ***";

        try {
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .withRegion(clientRegion)
                    .withCredentials(new ProfileCredentialsProvider())
                    .build();

            // Upload an object and encrypt it with SSE.
            uploadObjectWithSSEEncryption(s3Client, bucketName, keyNameToEncrypt);

            // Upload a new unencrypted object, then change its encryption state
            // to encrypted by making a copy.
            changeSSEEncryptionStatusByCopying(s3Client,
                    bucketName,
                    keyNameToCopyAndEncrypt,
                    copiedObjectKeyName);
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

    private static void uploadObjectWithSSEEncryption(AmazonS3 s3Client, String bucketName, String keyName) {
        String objectContent = "Test object encrypted with SSE";
        byte[] objectBytes = objectContent.getBytes();

        // Specify server-side encryption.
        ObjectMetadata objectMetadata = new ObjectMetadata();
        objectMetadata.setContentLength(objectBytes.length);
        objectMetadata.setSSEAlgorithm(ObjectMetadata.AES_256_SERVER_SIDE_ENCRYPTION);
        PutObjectRequest putRequest = new PutObjectRequest(bucketName,
                keyName,
                new ByteArrayInputStream(objectBytes),
                objectMetadata);

        // Upload the object and check its encryption status.
        PutObjectResult putResult = s3Client.putObject(putRequest);
        System.out.println("Object \"" + keyName + "\" uploaded with SSE.");
        printEncryptionStatus(putResult);
    }

    private static void changeSSEEncryptionStatusByCopying(AmazonS3 s3Client,
                                                           String bucketName,
                                                           String sourceKey,
                                                           String destKey) {
        // Upload a new, unencrypted object.
        PutObjectResult putResult = s3Client.putObject(bucketName, sourceKey, "Object example to encrypt by copying");
        System.out.println("Unencrypted object \"" + sourceKey + "\" uploaded.");
        printEncryptionStatus(putResult);

        // Make a copy of the object and use server-side encryption when storing the copy.
        CopyObjectRequest request = new CopyObjectRequest(bucketName,
                sourceKey,
                bucketName,
                destKey);
        ObjectMetadata objectMetadata = new ObjectMetadata();
        objectMetadata.setSSEAlgorithm(ObjectMetadata.AES_256_SERVER_SIDE_ENCRYPTION);
        request.setNewObjectMetadata(objectMetadata);

        // Perform the copy operation and display the copy's encryption status.
        CopyObjectResult response = s3Client.copyObject(request);
        System.out.println("Object \"" + destKey + "\" uploaded with SSE.");
        printEncryptionStatus(response);

        // Delete the original, unencrypted object, leaving only the encrypted copy in Amazon S3.
        s3Client.deleteObject(bucketName, sourceKey);
        System.out.println("Unencrypted object \"" + sourceKey + "\" deleted.");
    }

    private static void printEncryptionStatus(SSEResultBase response) {
        String encryptionStatus = response.getSSEAlgorithm();
        if (encryptionStatus == null) {
            encryptionStatus = "Not encrypted with SSE";
        }
        System.out.println("Object encryption status is: " + encryptionStatus);
    }
}
```

------
#### [ \.NET ]

When you upload an object, you can direct Amazon S3 to encrypt it\. To change the encryption state of an existing object, you make a copy of the object and delete the source object\. By default, the copy operation encrypts the target only if you explicitly request server\-side encryption of the target object\. To specify server\-side encryption in the `CopyObjectRequest`, add the following:

```
 ServerSideEncryptionMethod = ServerSideEncryptionMethod.AES256
```

For a working sample of how to copy an object, see [Using the AWS SDKs](copy-object.md#CopyingObjectsUsingSDKs)\. 

The following example uploads an object\. In the request, the example directs Amazon S3 to encrypt the object\. The example then retrieves object metadata and verifies the encryption method that was used\. For information about creating and testing a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\.

```
using Amazon;
using Amazon.S3;
using Amazon.S3.Model;
using System;
using System.Threading.Tasks;

namespace Amazon.DocSamples.S3
{
    class SpecifyServerSideEncryptionTest
    {
        private const string bucketName = "*** bucket name ***";
        private const string keyName = "*** key name for object created ***";
        // Specify your bucket region (an example region is shown).
        private static readonly RegionEndpoint bucketRegion = RegionEndpoint.USWest2;
        private static IAmazonS3 client;

        public static void Main()
        {
            client = new AmazonS3Client(bucketRegion);
            WritingAnObjectAsync().Wait();
        }

        static async Task WritingAnObjectAsync()
        {
            try
            {
                var putRequest = new PutObjectRequest
                {
                    BucketName = bucketName,
                    Key = keyName,
                    ContentBody = "sample text",
                    ServerSideEncryptionMethod = ServerSideEncryptionMethod.AES256
                };

                var putResponse = await client.PutObjectAsync(putRequest);

                // Determine the encryption state of an object.
                GetObjectMetadataRequest metadataRequest = new GetObjectMetadataRequest
                {
                    BucketName = bucketName,
                    Key = keyName
                };
                GetObjectMetadataResponse response = await client.GetObjectMetadataAsync(metadataRequest);
                ServerSideEncryptionMethod objectEncryption = response.ServerSideEncryptionMethod;

                Console.WriteLine("Encryption method used: {0}", objectEncryption.ToString());
            }
            catch (AmazonS3Exception e)
            {
                Console.WriteLine("Error encountered ***. Message:'{0}' when writing an object", e.Message);
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

This topic shows how to use classes from version 3 of the AWS SDK for PHP to add server\-side encryption to objects that you upload to Amazon Simple Storage Service \(Amazon S3\)\. It assumes that you are already following the instructions for [Using the AWS SDK for PHP and Running PHP Examples](UsingTheMPphpAPI.md) and have the AWS SDK for PHP properly installed\.

To upload an object to Amazon S3, use the [Aws\\S3\\S3Client::putObject\(\)](https://docs.aws.amazon.com/aws-sdk-php/v3/api/api-s3-2006-03-01.html#putobject) method\. To add the `x-amz-server-side-encryption` request header to your upload request, specify the `ServerSideEncryption` parameter with the value `AES256`, as shown in the following code example\. For information about server\-side encryption requests, see [Using the REST API](#SSEUsingRESTAPI)\.

```
 require 'vendor/autoload.php';

use Aws\S3\S3Client;

$bucket = '*** Your Bucket Name ***';
$keyname = '*** Your Object Key ***';

// $filepath should be an absolute path to a file on disk.
$filepath = '*** Your File Path ***';

$s3 = new S3Client([
    'version' => 'latest',
    'region'  => 'us-east-1'
]);

// Upload a file with server-side encryption.
$result = $s3->putObject([
    'Bucket'               => $bucket,
    'Key'                  => $keyname,
    'SourceFile'           => $filepath,
    'ServerSideEncryption' => 'AES256',
]);
```

In response, Amazon S3 returns the `x-amz-server-side-encryption` header with the value of the encryption algorithm that was used to encrypt your object's data\. 

When you upload large objects using the multipart upload API, you can specify server\-side encryption for the objects that you are uploading, as follows: 
+ When using the low\-level multipart upload API, specify server\-side encryption when you call the [ Aws\\S3\\S3Client::createMultipartUpload\(\)](https://docs.aws.amazon.com/aws-sdk-php/v3/api/api-s3-2006-03-01.html#createmultipartupload) method\. To add the `x-amz-server-side-encryption` request header to your request, specify the `array` parameter's `ServerSideEncryption` key with the value `AES256`\. For more information about the low\-level multipart upload API, see [Using the AWS SDKs \(low\-level\-level API\)](mpu-upload-object.md#mpu-upload-low-level)\.
+ When using the high\-level multipart upload API, specify server\-side encryption using the `ServerSideEncryption` parameter of the [CreateMultipartUpload](https://docs.aws.amazon.com/aws-sdk-php/v3/api/api-s3-2006-03-01.html#createmultipartupload) method\. For an example of using the `setOption()` method with the high\-level multipart upload API, see [Uploading an object using multipart upload](mpu-upload-object.md)\.

To determine the encryption state of an existing object, retrieve the object metadata by calling the [Aws\\S3\\S3Client::headObject\(\)](https://docs.aws.amazon.com/aws-sdk-php/v3/api/api-s3-2006-03-01.html#headobject) method as shown in the following PHP code example\.

```
 require 'vendor/autoload.php';

use Aws\S3\S3Client;

$bucket = '*** Your Bucket Name ***';
$keyname = '*** Your Object Key ***';
            
$s3 = new S3Client([
    'version' => 'latest',
    'region'  => 'us-east-1'
]);

// Check which server-side encryption algorithm is used.
$result = $s3->headObject([
    'Bucket' => $bucket,
    'Key'    => $keyname,
]);
echo $result['ServerSideEncryption'];
```

To change the encryption state of an existing object, make a copy of the object using the [Aws\\S3\\S3Client::copyObject\(\)](https://docs.aws.amazon.com/aws-sdk-php/v3/api/api-s3-2006-03-01.html#copyobject) method and delete the source object\. By default, `copyObject()` does not encrypt the target unless you explicitly request server\-side encryption of the destination object using the `ServerSideEncryption` parameter with the value `AES256`\. The following PHP code example makes a copy of an object and adds server\-side encryption to the copied object\.

```
 require 'vendor/autoload.php';

use Aws\S3\S3Client;

$sourceBucket = '*** Your Source Bucket Name ***';
$sourceKeyname = '*** Your Source Object Key ***';

$targetBucket = '*** Your Target Bucket Name ***';
$targetKeyname = '*** Your Target Object Key ***';

$s3 = new S3Client([
    'version' => 'latest',
    'region'  => 'us-east-1'
]);

// Copy an object and add server-side encryption.
$s3->copyObject([
    'Bucket'               => $targetBucket,
    'Key'                  => $targetKeyname,
    'CopySource'           => "{$sourceBucket}/{$sourceKeyname}",
    'ServerSideEncryption' => 'AES256',
]);
```
+ [ AWS SDK for PHP for Amazon S3 Aws\\S3\\S3Client Class](https://docs.aws.amazon.com/aws-sdk-php/v3/api/class-Aws.S3.S3Client.html) 
+ [AWS SDK for PHP Documentation](http://aws.amazon.com/documentation/sdk-for-php/)

------
#### [ Ruby ]

When using the AWS SDK for Ruby to upload an object, you can specify that the object be stored encrypted at rest with server\-side encryption \(SSE\)\. When you read the object back, it is automatically decrypted\.

The following AWS SDK for Ruby – Version 3 example demonstrates how to specify that a file uploaded to Amazon S3 be encrypted at rest\.

```
require 'aws-sdk-s3'

# Uploads a file to an Amazon S3 bucket and then encrypts the file server-side
#   by using the 256-bit Advanced Encryption Standard (AES-256) block cipher.
#
# Prerequisites:
#
# - An Amazon S3 bucket.
#
# @param s3_client [Aws::S3::Client] An initialized Amazon S3 client.
# @param bucket_name [String] The name of the bucket.
# @param object_key [String] The name for the uploaded object.
# @param object_content [String] The content to upload into the object.
# @return [Boolean] true if the file was successfully uploaded and then
#   encrypted; otherwise, false.
# @example
#   exit 1 unless upload_file_encrypted_aes256_at_rest?(
#     Aws::S3::Client.new(region: 'us-east-1'),
#     'doc-example-bucket',
#     'my-file.txt',
#     'This is the content of my-file.txt.'
#   )
def upload_file_encrypted_aes256_at_rest?(
  s3_client,
  bucket_name,
  object_key,
  object_content
)
  s3_client.put_object(
    bucket: bucket_name,
    key: object_key,
    body: object_content,
    server_side_encryption: 'AES256'
  )
  return true
rescue StandardError => e
  puts "Error uploading object: #{e.message}"
  return false
end
```

For an example that shows how to upload an object without SSE, see [Uploading objects](upload-objects.md)\.

The following code example demonstrates how to determine the encryption state of an existing object\.

```
require 'aws-sdk-s3'

# Gets the server-side encryption state of an object in an Amazon S3 bucket.
#
# Prerequisites:
#
# - An Amazon S3 bucket.
# - An object within that bucket.
#
# @param s3_client [Aws::S3::Client] An initialized Amazon S3 client.
# @param bucket_name [String] The bucket's name.
# @param object_key [String] The object's key.
# @return [String] The server-side encryption state.
# @example
#   s3_client = Aws::S3::Client.new(region: 'us-east-1')
#   puts get_server_side_encryption_state(
#     s3_client,
#     'doc-example-bucket',
#     'my-file.txt'
#   )
def get_server_side_encryption_state(s3_client, bucket_name, object_key)
  response = s3_client.get_object(
    bucket: bucket_name,
    key: object_key
  )
  encryption_state = response.server_side_encryption
  encryption_state.nil? ? 'not set' : encryption_state
rescue StandardError => e
  "unknown or error: #{e.message}"
end
```

If server\-side encryption is not used for the object that is stored in Amazon S3, the method returns null\.

To change the encryption state of an existing object, make a copy of the object and delete the source object\. By default, the copy methods do not encrypt the target unless you explicitly request server\-side encryption\. You can request the encryption of the target object by specifying the `server_side_encryption` value in the options hash argument as shown in the following Ruby code example\. The code example demonstrates how to copy an object and encrypt the copy\. 

```
require 'aws-sdk-s3'

# Copies an object from one Amazon S3 bucket to another,
#   changing the object's server-side encryption state during 
#   the copy operation.
#
# Prerequisites:
#
# - A bucket containing an object to be copied.
# - A separate bucket to copy the object into.
#
# @param s3_client [Aws::S3::Client] An initialized Amazon S3 client.
# @param source_bucket_name [String] The source bucket's name.
# @param source_object_key [String] The name of the object to be copied.
# @param target_bucket_name [String] The target bucket's name.
# @param target_object_key [String] The name of the copied object.
# @param encryption_type [String] The server-side encryption type for
#   the copied object.
# @return [Boolean] true if the object was copied with the specified
#   server-side encryption; otherwise, false.
# @example
#   s3_client = Aws::S3::Client.new(region: 'us-east-1')
#   if object_copied_with_encryption?(
#     s3_client,
#     'doc-example-bucket1',
#     'my-source-file.txt',
#     'doc-example-bucket2',
#     'my-target-file.txt',
#     'AES256'
#   )
#     puts 'Copied.'
#   else
#     puts 'Not copied.'
#   end
def object_copied_with_encryption?(
  s3_client,
  source_bucket_name,
  source_object_key,
  target_bucket_name,
  target_object_key,
  encryption_type
)
  response = s3_client.copy_object(
    bucket: target_bucket_name,
    copy_source: source_bucket_name + '/' + source_object_key,
    key: target_object_key,
    server_side_encryption: encryption_type
  )
  return true if response.copy_object_result
rescue StandardError => e
  puts "Error while copying object: #{e.message}"
end
```

For a sample of how to copy an object without encryption, see [Copying objects](copy-object.md)\. 

------