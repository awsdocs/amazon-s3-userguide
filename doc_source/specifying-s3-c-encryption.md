# Specifying server\-side encryption with customer\-provided keys \(SSE\-C\)<a name="specifying-s3-c-encryption"></a>

At the time of object creation with the REST API, you can specify server\-side encryption with customer\-provided encryption keys \(SSE\-C\)\. When you use SSE\-C, you must provide encryption key information using the following request headers\. 


|  Name  |  Description  | 
| --- | --- | 
| x\-amz\-server\-side​\-encryption​\-customer\-algorithm  |  Use this header to specify the encryption algorithm\. The header value must be "AES256"\.   | 
| x\-amz\-server\-side​\-encryption​\-customer\-key  |  Use this header to provide the 256\-bit, base64\-encoded encryption key for Amazon S3 to use to encrypt or decrypt your data\.   | 
| x\-amz\-server\-side​\-encryption​\-customer\-key\-MD5  |  Use this header to provide the base64\-encoded 128\-bit MD5 digest of the encryption key according to [RFC 1321](http://tools.ietf.org/html/rfc1321)\. Amazon S3 uses this header for a message integrity check to ensure that the encryption key was transmitted without error\.  | 

You can use AWS SDK wrapper libraries to add these headers to your request\. If you need to, you can make the Amazon S3 REST API calls directly in your application\. 

**Note**  
You cannot use the Amazon S3 console to upload an object and request SSE\-C\. You also cannot use the console to update \(for example, change the storage class or add metadata\) an existing object stored using SSE\-C\.

## Presigned URLs and SSE\-C<a name="ssec-and-presignedurl"></a>

You can generate a presigned URL that can be used for operations such as upload a new object, retrieve an existing object, or object metadata\. Presigned URLs support the SSE\-C as follows:
+ When creating a presigned URL, you must specify the algorithm using the `x-amz-server-side​-encryption​-customer-algorithm` in the signature calculation\.
+ When using the presigned URL to upload a new object, retrieve an existing object, or retrieve only object metadata, you must provide all the encryption headers in your client application\. 
**Note**  
For non\-SSE\-C objects, you can generate a presigned URL and directly paste that into a browser, for example to access the data\.   
However, this is not true for SSE\-C objects because in addition to the presigned URL, you also need to include HTTP headers that are specific to SSE\-C objects\. Therefore, you can use the presigned URL for SSE\-C objects only programmatically\.

## Using the REST API<a name="sse-c-using-rest-api"></a>

### Amazon S3 rest APIs that support SSE\-C<a name="sse-c-supported-apis"></a>

The following Amazon S3 APIs support server\-side encryption with customer\-provided encryption keys \(SSE\-C\)\.
+ **GET operation** — When retrieving objects using the GET API \(see [GET Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectGET.html)\), you can specify the request headers\.
+ **HEAD operation** — To retrieve object metadata using the HEAD API \(see [HEAD Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectHEAD.html)\), you can specify these request headers\.
+ **PUT operation** — When uploading data using the PUT Object API \(see [PUT Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPUT.html)\), you can specify these request headers\. 
+ **Multipart Upload **— When uploading large objects using the multipart upload API, you can specify these headers\. You specify these headers in the initiate request \(see [Initiate Multipart Upload](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadInitiate.html)\) and each subsequent part upload request \(see [Upload Part](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadUploadPart.html) or 

  [Upload Part \- Copy](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadUploadPartCopy.html)\)

  \)\. For each part upload request, the encryption information must be the same as what you provided in the initiate multipart upload request\.
+ **POST operation **— When using a POST operation to upload an object \(see [POST Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOST.html)\), instead of the request headers, you provide the same information in the form fields\.
+ **Copy operation **— When you copy an object \(see [PUT Object \- Copy](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectCOPY.html)\), you have both a source object and a target object:
  + If you want the target object encrypted using server\-side encryption with AWS managed keys, you must provide the `x-amz-server-side​-encryption` request header\.
  +  If you want the target object encrypted using SSE\-C, you must provide encryption information using the three headers described in the preceding table\.
  +  If the source object is encrypted using SSE\-C, you must provide encryption key information using the following headers so that Amazon S3 can decrypt the object for copying\.    
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/specifying-s3-c-encryption.html)

## Using the AWS SDKs to specify SSE\-C for PUT, GET, Head, and Copy operations<a name="sse-c-using-sdks"></a>

The following examples show how to request server\-side encryption with customer\-provided keys \(SSE\-C\) for objects\. The examples perform the following operations\. Each operation shows how to specify SSE\-C\-related headers in the request:
+ **Put object**—Uploads an object and requests server\-side encryption using a customer\-provided encryption key\.
+ **Get object**—Downloads the object uploaded in the previous step\. In the request, you provide the same encryption information you provided when you uploaded the object\. Amazon S3 needs this information to decrypt the object so that it can return it to you\.
+ **Get object metadata**—Retrieves the object's metadata\. You provide the same encryption information used when the object was created\.
+ **Copy object**—Makes a copy of the previously\-uploaded object\. Because the source object is stored using SSE\-C, you must provide its encryption information in your copy request\. By default, Amazon S3 encrypts the copy of the object only if you explicitly request it\. This example directs Amazon S3 to store an encrypted copy of the object\.

------
#### [ Java ]

**Note**  
This example shows how to upload an object in a single operation\. When using the Multipart Upload API to upload large objects, you provide encryption information in the same way shown in this example\. For examples of multipart uploads that use the AWS SDK for Java, see [Uploading an object using multipart upload](mpu-upload-object.md)\.

To add the required encryption information, you include an `SSECustomerKey` in your request\. For more information about the `SSECustomerKey` class, see the REST API section\.

For information about SSE\-C, see [Protecting data using server\-side encryption with customer\-provided encryption keys \(SSE\-C\)](ServerSideEncryptionCustomerKeys.md)\. For instructions on creating and testing a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\. 

**Example**  

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.*;

import javax.crypto.KeyGenerator;
import java.io.BufferedReader;
import java.io.File;
import java.io.IOException;
import java.io.InputStreamReader;
import java.security.NoSuchAlgorithmException;
import java.security.SecureRandom;

public class ServerSideEncryptionUsingClientSideEncryptionKey {
    private static SSECustomerKey SSE_KEY;
    private static AmazonS3 S3_CLIENT;
    private static KeyGenerator KEY_GENERATOR;

    public static void main(String[] args) throws IOException, NoSuchAlgorithmException {
        Regions clientRegion = Regions.DEFAULT_REGION;
        String bucketName = "*** Bucket name ***";
        String keyName = "*** Key name ***";
        String uploadFileName = "*** File path ***";
        String targetKeyName = "*** Target key name ***";

        // Create an encryption key.
        KEY_GENERATOR = KeyGenerator.getInstance("AES");
        KEY_GENERATOR.init(256, new SecureRandom());
        SSE_KEY = new SSECustomerKey(KEY_GENERATOR.generateKey());

        try {
            S3_CLIENT = AmazonS3ClientBuilder.standard()
                    .withCredentials(new ProfileCredentialsProvider())
                    .withRegion(clientRegion)
                    .build();

            // Upload an object.
            uploadObject(bucketName, keyName, new File(uploadFileName));

            // Download the object.
            downloadObject(bucketName, keyName);

            // Verify that the object is properly encrypted by attempting to retrieve it
            // using the encryption key.
            retrieveObjectMetadata(bucketName, keyName);

            // Copy the object into a new object that also uses SSE-C.
            copyObject(bucketName, keyName, targetKeyName);
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

    private static void uploadObject(String bucketName, String keyName, File file) {
        PutObjectRequest putRequest = new PutObjectRequest(bucketName, keyName, file).withSSECustomerKey(SSE_KEY);
        S3_CLIENT.putObject(putRequest);
        System.out.println("Object uploaded");
    }

    private static void downloadObject(String bucketName, String keyName) throws IOException {
        GetObjectRequest getObjectRequest = new GetObjectRequest(bucketName, keyName).withSSECustomerKey(SSE_KEY);
        S3Object object = S3_CLIENT.getObject(getObjectRequest);

        System.out.println("Object content: ");
        displayTextInputStream(object.getObjectContent());
    }

    private static void retrieveObjectMetadata(String bucketName, String keyName) {
        GetObjectMetadataRequest getMetadataRequest = new GetObjectMetadataRequest(bucketName, keyName)
                .withSSECustomerKey(SSE_KEY);
        ObjectMetadata objectMetadata = S3_CLIENT.getObjectMetadata(getMetadataRequest);
        System.out.println("Metadata retrieved. Object size: " + objectMetadata.getContentLength());
    }

    private static void copyObject(String bucketName, String keyName, String targetKeyName)
            throws NoSuchAlgorithmException {
        // Create a new encryption key for target so that the target is saved using SSE-C.
        SSECustomerKey newSSEKey = new SSECustomerKey(KEY_GENERATOR.generateKey());

        CopyObjectRequest copyRequest = new CopyObjectRequest(bucketName, keyName, bucketName, targetKeyName)
                .withSourceSSECustomerKey(SSE_KEY)
                .withDestinationSSECustomerKey(newSSEKey);

        S3_CLIENT.copyObject(copyRequest);
        System.out.println("Object copied");
    }

    private static void displayTextInputStream(S3ObjectInputStream input) throws IOException {
        // Read one line at a time from the input stream and display each line.
        BufferedReader reader = new BufferedReader(new InputStreamReader(input));
        String line;
        while ((line = reader.readLine()) != null) {
            System.out.println(line);
        }
        System.out.println();
    }
}
```

------
#### [ \.NET ]

**Note**  
For examples of uploading large objects using the multipart upload API, see [Uploading an object using multipart upload](mpu-upload-object.md) and [Using the AWS SDKs \(low\-level\-level API\)](mpu-upload-object.md#mpu-upload-low-level)\.

For information about SSE\-C, see [Protecting data using server\-side encryption with customer\-provided encryption keys \(SSE\-C\)](ServerSideEncryptionCustomerKeys.md)\)\. For information about creating and testing a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\. 

**Example**  

```
using Amazon;
using Amazon.S3;
using Amazon.S3.Model;
using System;
using System.IO;
using System.Security.Cryptography;
using System.Threading.Tasks;

namespace Amazon.DocSamples.S3
{
    class SSEClientEncryptionKeyObjectOperationsTest
    {
        private const string bucketName = "*** bucket name ***"; 
        private const string keyName = "*** key name for new object created ***"; 
        private const string copyTargetKeyName = "*** key name for object copy ***";
        // Specify your bucket region (an example region is shown).
        private static readonly RegionEndpoint bucketRegion = RegionEndpoint.USWest2;
        private static IAmazonS3 client;

        public static void Main()
        {
            client = new AmazonS3Client(bucketRegion);
            ObjectOpsUsingClientEncryptionKeyAsync().Wait();
        }
        private static async Task ObjectOpsUsingClientEncryptionKeyAsync()
        {
            try
            {
                // Create an encryption key.
                Aes aesEncryption = Aes.Create();
                aesEncryption.KeySize = 256;
                aesEncryption.GenerateKey();
                string base64Key = Convert.ToBase64String(aesEncryption.Key);

                // 1. Upload the object.
                PutObjectRequest putObjectRequest = await UploadObjectAsync(base64Key);
                // 2. Download the object and verify that its contents matches what you uploaded.
                await DownloadObjectAsync(base64Key, putObjectRequest);
                // 3. Get object metadata and verify that the object uses AES-256 encryption.
                await GetObjectMetadataAsync(base64Key);
                // 4. Copy both the source and target objects using server-side encryption with 
                //    a customer-provided encryption key.
                await CopyObjectAsync(aesEncryption, base64Key);
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

        private static async Task<PutObjectRequest> UploadObjectAsync(string base64Key)
        {
            PutObjectRequest putObjectRequest = new PutObjectRequest
            {
                BucketName = bucketName,
                Key = keyName,
                ContentBody = "sample text",
                ServerSideEncryptionCustomerMethod = ServerSideEncryptionCustomerMethod.AES256,
                ServerSideEncryptionCustomerProvidedKey = base64Key
            };
            PutObjectResponse putObjectResponse = await client.PutObjectAsync(putObjectRequest);
            return putObjectRequest;
        }
        private static async Task DownloadObjectAsync(string base64Key, PutObjectRequest putObjectRequest)
        {
            GetObjectRequest getObjectRequest = new GetObjectRequest
            {
                BucketName = bucketName,
                Key = keyName,
                // Provide encryption information for the object stored in Amazon S3.
                ServerSideEncryptionCustomerMethod = ServerSideEncryptionCustomerMethod.AES256,
                ServerSideEncryptionCustomerProvidedKey = base64Key
            };

            using (GetObjectResponse getResponse = await client.GetObjectAsync(getObjectRequest))
            using (StreamReader reader = new StreamReader(getResponse.ResponseStream))
            {
                string content = reader.ReadToEnd();
                if (String.Compare(putObjectRequest.ContentBody, content) == 0)
                    Console.WriteLine("Object content is same as we uploaded");
                else
                    Console.WriteLine("Error...Object content is not same.");

                if (getResponse.ServerSideEncryptionCustomerMethod == ServerSideEncryptionCustomerMethod.AES256)
                    Console.WriteLine("Object encryption method is AES256, same as we set");
                else
                    Console.WriteLine("Error...Object encryption method is not the same as AES256 we set");

                // Assert.AreEqual(putObjectRequest.ContentBody, content);
                // Assert.AreEqual(ServerSideEncryptionCustomerMethod.AES256, getResponse.ServerSideEncryptionCustomerMethod);
            }
        }
        private static async Task GetObjectMetadataAsync(string base64Key)
        {
            GetObjectMetadataRequest getObjectMetadataRequest = new GetObjectMetadataRequest
            {
                BucketName = bucketName,
                Key = keyName,

                // The object stored in Amazon S3 is encrypted, so provide the necessary encryption information.
                ServerSideEncryptionCustomerMethod = ServerSideEncryptionCustomerMethod.AES256,
                ServerSideEncryptionCustomerProvidedKey = base64Key
            };

            GetObjectMetadataResponse getObjectMetadataResponse = await client.GetObjectMetadataAsync(getObjectMetadataRequest);
            Console.WriteLine("The object metadata show encryption method used is: {0}", getObjectMetadataResponse.ServerSideEncryptionCustomerMethod);
            // Assert.AreEqual(ServerSideEncryptionCustomerMethod.AES256, getObjectMetadataResponse.ServerSideEncryptionCustomerMethod);
        }
        private static async Task CopyObjectAsync(Aes aesEncryption, string base64Key)
        {
            aesEncryption.GenerateKey();
            string copyBase64Key = Convert.ToBase64String(aesEncryption.Key);

            CopyObjectRequest copyRequest = new CopyObjectRequest
            {
                SourceBucket = bucketName,
                SourceKey = keyName,
                DestinationBucket = bucketName,
                DestinationKey = copyTargetKeyName,
                // Information about the source object's encryption.
                CopySourceServerSideEncryptionCustomerMethod = ServerSideEncryptionCustomerMethod.AES256,
                CopySourceServerSideEncryptionCustomerProvidedKey = base64Key,
                // Information about the target object's encryption.
                ServerSideEncryptionCustomerMethod = ServerSideEncryptionCustomerMethod.AES256,
                ServerSideEncryptionCustomerProvidedKey = copyBase64Key
            };
            await client.CopyObjectAsync(copyRequest);
        }
    }
}
```

------

## Using the AWS SDKs to specify SSE\-C for multipart uploads<a name="sse-c-using-sdks-multipart-uploads"></a>

The example in the preceding section shows how to request server\-side encryption with customer\-provided key \(SSE\-C\) in the PUT, GET, Head, and Copy operations\. This section describes other Amazon S3 APIs that support SSE\-C\.

------
#### [ Java ]

To upload large objects, you can use multipart upload API \(see [Uploading and copying objects using multipart upload](mpuoverview.md)\)\. You can use either high\-level or low\-level APIs to upload large objects\. These APIs support encryption\-related headers in the request\.
+ When using the high\-level `TransferManager` API, you provide the encryption\-specific headers in the `PutObjectRequest` \(see [Uploading an object using multipart upload](mpu-upload-object.md)\)\. 
+ When using the low\-level API, you provide encryption\-related information in the `InitiateMultipartUploadRequest`, followed by identical encryption information in each `UploadPartRequest`\. You do not need to provide any encryption\-specific headers in your `CompleteMultipartUploadRequest`\. For examples, see [Using the AWS SDKs \(low\-level\-level API\)](mpu-upload-object.md#mpu-upload-low-level)\. 

The following example uses `TransferManager` to create objects and shows how to provide SSE\-C related information\. The example does the following:
+ Creates an object using the `TransferManager.upload()` method\. In the `PutObjectRequest` instance, you provide encryption key information to request\. Amazon S3 encrypts the object using the customer\-provided encryption key\.
+ Makes a copy of the object by calling the `TransferManager.copy()` method\. The example directs Amazon S3 to encrypt the object copy using a new `SSECustomerKey`\. Because the source object is encrypted using SSE\-C, the `CopyObjectRequest` also provides the encryption key of the source object so that Amazon S3 can decrypt the object before copying it\. 

**Example**  

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.CopyObjectRequest;
import com.amazonaws.services.s3.model.PutObjectRequest;
import com.amazonaws.services.s3.model.SSECustomerKey;
import com.amazonaws.services.s3.transfer.Copy;
import com.amazonaws.services.s3.transfer.TransferManager;
import com.amazonaws.services.s3.transfer.TransferManagerBuilder;
import com.amazonaws.services.s3.transfer.Upload;

import javax.crypto.KeyGenerator;
import java.io.File;
import java.security.SecureRandom;

public class ServerSideEncryptionCopyObjectUsingHLwithSSEC {

    public static void main(String[] args) throws Exception {
        Regions clientRegion = Regions.DEFAULT_REGION;
        String bucketName = "*** Bucket name ***";
        String fileToUpload = "*** File path ***";
        String keyName = "*** New object key name ***";
        String targetKeyName = "*** Key name for object copy ***";

        try {
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .withRegion(clientRegion)
                    .withCredentials(new ProfileCredentialsProvider())
                    .build();
            TransferManager tm = TransferManagerBuilder.standard()
                    .withS3Client(s3Client)
                    .build();

            // Create an object from a file.
            PutObjectRequest putObjectRequest = new PutObjectRequest(bucketName, keyName, new File(fileToUpload));

            // Create an encryption key.
            KeyGenerator keyGenerator = KeyGenerator.getInstance("AES");
            keyGenerator.init(256, new SecureRandom());
            SSECustomerKey sseCustomerEncryptionKey = new SSECustomerKey(keyGenerator.generateKey());

            // Upload the object. TransferManager uploads asynchronously, so this call returns immediately.
            putObjectRequest.setSSECustomerKey(sseCustomerEncryptionKey);
            Upload upload = tm.upload(putObjectRequest);

            // Optionally, wait for the upload to finish before continuing.
            upload.waitForCompletion();
            System.out.println("Object created.");

            // Copy the object and store the copy using SSE-C with a new key.
            CopyObjectRequest copyObjectRequest = new CopyObjectRequest(bucketName, keyName, bucketName, targetKeyName);
            SSECustomerKey sseTargetObjectEncryptionKey = new SSECustomerKey(keyGenerator.generateKey());
            copyObjectRequest.setSourceSSECustomerKey(sseCustomerEncryptionKey);
            copyObjectRequest.setDestinationSSECustomerKey(sseTargetObjectEncryptionKey);

            // Copy the object. TransferManager copies asynchronously, so this call returns immediately.
            Copy copy = tm.copy(copyObjectRequest);

            // Optionally, wait for the upload to finish before continuing.
            copy.waitForCompletion();
            System.out.println("Copy complete.");
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

To upload large objects, you can use multipart upload API \(see [Uploading and copying objects using multipart upload](mpuoverview.md)\)\. AWS SDK for \.NET provides both high\-level or low\-level APIs to upload large objects\. These APIs support encryption\-related headers in the request\.
+ When using high\-level `Transfer-Utility `API, you provide the encryption\-specific headers in the `TransferUtilityUploadRequest` as shown\. For code examples, see [Uploading an object using multipart upload](mpu-upload-object.md)\.

  ```
  TransferUtilityUploadRequest request = new TransferUtilityUploadRequest()
  {
      FilePath = filePath,
      BucketName = existingBucketName,
      Key = keyName,
      // Provide encryption information.
      ServerSideEncryptionCustomerMethod = ServerSideEncryptionCustomerMethod.AES256,
      ServerSideEncryptionCustomerProvidedKey = base64Key,
  };
  ```
+ When using the low\-level API, you provide encryption\-related information in the initiate multipart upload request, followed by identical encryption information in the subsequent upload part requests\. You do not need to provide any encryption\-specific headers in your complete multipart upload request\. For examples, see [Using the AWS SDKs \(low\-level\-level API\)](mpu-upload-object.md#mpu-upload-low-level)\.

  The following is a low\-level multipart upload example that makes a copy of an existing large object\. In the example, the object to be copied is stored in Amazon S3 using SSE\-C, and you want to save the target object also using SSE\-C\. In the example, you do the following:
  + Initiate a multipart upload request by providing an encryption key and related information\.
  + Provide source and target object encryption keys and related information in the `CopyPartRequest`\.
  + Obtain the size of the source object to be copied by retrieving the object metadata\.
  + Upload the objects in 5 MB parts\.  
**Example**  

  ```
  using Amazon;
  using Amazon.S3;
  using Amazon.S3.Model;
  using System;
  using System.Collections.Generic;
  using System.IO;
  using System.Security.Cryptography;
  using System.Threading.Tasks;
  
  namespace Amazon.DocSamples.S3
  {
      class SSECLowLevelMPUcopyObjectTest
      {
          private const string existingBucketName = "*** bucket name ***";
          private const string sourceKeyName      = "*** source object key name ***"; 
          private const string targetKeyName      = "*** key name for the target object ***";
          private const string filePath           = @"*** file path ***";
          // Specify your bucket region (an example region is shown).
          private static readonly RegionEndpoint bucketRegion = RegionEndpoint.USWest2;
          private static IAmazonS3 s3Client;
          static void Main()
          {
              s3Client = new AmazonS3Client(bucketRegion);
              CopyObjClientEncryptionKeyAsync().Wait();
          }
  
          private static async Task CopyObjClientEncryptionKeyAsync()
          {
              Aes aesEncryption = Aes.Create();
              aesEncryption.KeySize = 256;
              aesEncryption.GenerateKey();
              string base64Key = Convert.ToBase64String(aesEncryption.Key);
  
              await CreateSampleObjUsingClientEncryptionKeyAsync(base64Key, s3Client);
  
              await CopyObjectAsync(s3Client, base64Key);
          }
          private static async Task CopyObjectAsync(IAmazonS3 s3Client, string base64Key)
          {
              List<CopyPartResponse> uploadResponses = new List<CopyPartResponse>();
  
              // 1. Initialize.
              InitiateMultipartUploadRequest initiateRequest = new InitiateMultipartUploadRequest
              {
                  BucketName = existingBucketName,
                  Key = targetKeyName,
                  ServerSideEncryptionCustomerMethod = ServerSideEncryptionCustomerMethod.AES256,
                  ServerSideEncryptionCustomerProvidedKey = base64Key,
              };
  
              InitiateMultipartUploadResponse initResponse =
                  await s3Client.InitiateMultipartUploadAsync(initiateRequest);
  
              // 2. Upload Parts.
              long partSize = 5 * (long)Math.Pow(2, 20); // 5 MB
              long firstByte = 0;
              long lastByte = partSize;
  
              try
              {
                  // First find source object size. Because object is stored encrypted with
                  // customer provided key you need to provide encryption information in your request.
                  GetObjectMetadataRequest getObjectMetadataRequest = new GetObjectMetadataRequest()
                  {
                      BucketName = existingBucketName,
                      Key = sourceKeyName,
                      ServerSideEncryptionCustomerMethod = ServerSideEncryptionCustomerMethod.AES256,
                      ServerSideEncryptionCustomerProvidedKey = base64Key // " * **source object encryption key ***"
                  };
  
                  GetObjectMetadataResponse getObjectMetadataResponse = await s3Client.GetObjectMetadataAsync(getObjectMetadataRequest);
  
                  long filePosition = 0;
                  for (int i = 1; filePosition < getObjectMetadataResponse.ContentLength; i++)
                  {
                      CopyPartRequest copyPartRequest = new CopyPartRequest
                      {
                          UploadId = initResponse.UploadId,
                          // Source.
                          SourceBucket = existingBucketName,
                          SourceKey = sourceKeyName,
                          // Source object is stored using SSE-C. Provide encryption information.
                          CopySourceServerSideEncryptionCustomerMethod = ServerSideEncryptionCustomerMethod.AES256,
                          CopySourceServerSideEncryptionCustomerProvidedKey = base64Key, //"***source object encryption key ***",
                          FirstByte = firstByte,
                          // If the last part is smaller then our normal part size then use the remaining size.
                          LastByte = lastByte > getObjectMetadataResponse.ContentLength ?
                              getObjectMetadataResponse.ContentLength - 1 : lastByte,
  
                          // Target.
                          DestinationBucket = existingBucketName,
                          DestinationKey = targetKeyName,
                          PartNumber = i,
                          // Encryption information for the target object.
                          ServerSideEncryptionCustomerMethod = ServerSideEncryptionCustomerMethod.AES256,
                          ServerSideEncryptionCustomerProvidedKey = base64Key
                      };
                      uploadResponses.Add(await s3Client.CopyPartAsync(copyPartRequest));
                      filePosition += partSize;
                      firstByte += partSize;
                      lastByte += partSize;
                  }
  
                  // Step 3: complete.
                  CompleteMultipartUploadRequest completeRequest = new CompleteMultipartUploadRequest
                  {
                      BucketName = existingBucketName,
                      Key = targetKeyName,
                      UploadId = initResponse.UploadId,
                  };
                  completeRequest.AddPartETags(uploadResponses);
  
                  CompleteMultipartUploadResponse completeUploadResponse =
                      await s3Client.CompleteMultipartUploadAsync(completeRequest);
              }
              catch (Exception exception)
              {
                  Console.WriteLine("Exception occurred: {0}", exception.Message);
                  AbortMultipartUploadRequest abortMPURequest = new AbortMultipartUploadRequest
                  {
                      BucketName = existingBucketName,
                      Key = targetKeyName,
                      UploadId = initResponse.UploadId
                  };
                  s3Client.AbortMultipartUpload(abortMPURequest);
              }
          }
          private static async Task CreateSampleObjUsingClientEncryptionKeyAsync(string base64Key, IAmazonS3 s3Client)
          {
              // List to store upload part responses.
              List<UploadPartResponse> uploadResponses = new List<UploadPartResponse>();
  
              // 1. Initialize.
              InitiateMultipartUploadRequest initiateRequest = new InitiateMultipartUploadRequest
              {
                  BucketName = existingBucketName,
                  Key = sourceKeyName,
                  ServerSideEncryptionCustomerMethod = ServerSideEncryptionCustomerMethod.AES256,
                  ServerSideEncryptionCustomerProvidedKey = base64Key
              };
  
              InitiateMultipartUploadResponse initResponse =
                 await s3Client.InitiateMultipartUploadAsync(initiateRequest);
  
              // 2. Upload Parts.
              long contentLength = new FileInfo(filePath).Length;
              long partSize = 5 * (long)Math.Pow(2, 20); // 5 MB
  
              try
              {
                  long filePosition = 0;
                  for (int i = 1; filePosition < contentLength; i++)
                  {
                      UploadPartRequest uploadRequest = new UploadPartRequest
                      {
                          BucketName = existingBucketName,
                          Key = sourceKeyName,
                          UploadId = initResponse.UploadId,
                          PartNumber = i,
                          PartSize = partSize,
                          FilePosition = filePosition,
                          FilePath = filePath,
                          ServerSideEncryptionCustomerMethod = ServerSideEncryptionCustomerMethod.AES256,
                          ServerSideEncryptionCustomerProvidedKey = base64Key
                      };
  
                      // Upload part and add response to our list.
                      uploadResponses.Add(await s3Client.UploadPartAsync(uploadRequest));
  
                      filePosition += partSize;
                  }
  
                  // Step 3: complete.
                  CompleteMultipartUploadRequest completeRequest = new CompleteMultipartUploadRequest
                  {
                      BucketName = existingBucketName,
                      Key = sourceKeyName,
                      UploadId = initResponse.UploadId,
                      //PartETags = new List<PartETag>(uploadResponses)
  
                  };
                  completeRequest.AddPartETags(uploadResponses);
  
                  CompleteMultipartUploadResponse completeUploadResponse =
                      await s3Client.CompleteMultipartUploadAsync(completeRequest);
  
              }
              catch (Exception exception)
              {
                  Console.WriteLine("Exception occurred: {0}", exception.Message);
                  AbortMultipartUploadRequest abortMPURequest = new AbortMultipartUploadRequest
                  {
                      BucketName = existingBucketName,
                      Key = sourceKeyName,
                      UploadId = initResponse.UploadId
                  };
                  await s3Client.AbortMultipartUploadAsync(abortMPURequest);
              }
          }
      }
  }
  ```

------