# Deleting a single object<a name="delete-objects"></a>

You can use the Amazon S3 console or the DELETE API to delete a single existing object from an S3 bucket\.

Because all objects in your S3 bucket incur storage costs, you should delete objects that you no longer need\. For example, if you are collecting log files, it's a good idea to delete them when they're no longer needed\. You can set up a lifecycle rule to automatically delete objects such as log files\. For more information, see [Setting lifecycle configuration on a bucket](how-to-set-lifecycle-configuration-intro.md)\.

For information about Amazon S3 features and pricing, see [Amazon S3 pricing](http://aws.amazon.com/s3/pricing)\.

## Using the S3 console<a name="delete-object-console"></a>

Follow these steps to use the Amazon S3 console to delete a single object from a bucket\.

**To delete an object**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Bucket name** list, choose the name of the bucket that you want to delete an object from\.

1. To delete an object in a versioning\-enabled bucket with versioning: 
   + **Off**, Amazon S3 creates a delete marker\. To delete the object, select the object, and choose **delete** and confirm your choice by typing **delete** in the text field\.
   + **On**, Amazon S3 will permanently delete the object version\. Select the object version that you want to delete, and choose **delete** and confirm your choice by typing **permenently delete** in the text field\.

## Using the AWS SDKs<a name="DeletingOneObject"></a>

The following examples show how you can use the AWS SDKs to delete an object from a bucket\. For more information, see [DELETE Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectDELETE.html) in the *Amazon Simple Storage Service API Reference*

If you have S3 Versioning enabled on the bucket, you have the following options:
+ Delete a specific object version by specifying a version ID\.
+ Delete an object without specifying a version ID, in which case Amazon S3 adds a delete marker to the object\.

For more information about S3 Versioning, see [Using versioning in S3 buckets](Versioning.md)\. 

------
#### [ Java ]

**Example 1: Deleting an object \(non\-versioned bucket\)**  
The following example assumes that the bucket is not versioning\-enabled and the object doesn't have any version IDs\. In the delete request, you specify only the object key and not a version ID\.   
For instructions on creating and testing a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\.  

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.DeleteObjectRequest;

import java.io.IOException;

public class DeleteObjectNonVersionedBucket {

    public static void main(String[] args) throws IOException {
        Regions clientRegion = Regions.DEFAULT_REGION;
        String bucketName = "*** Bucket name ***";
        String keyName = "*** Key name ****";

        try {
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .withCredentials(new ProfileCredentialsProvider())
                    .withRegion(clientRegion)
                    .build();

            s3Client.deleteObject(new DeleteObjectRequest(bucketName, keyName));
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

**Example 2: Deleting an object \(versioned bucket\)**  
The following example deletes an object from a versioned bucket\. The example deletes a specific object version by specifying the object key name and version ID\.   
The example does the following:  

1. Adds a sample object to the bucket\. Amazon S3 returns the version ID of the newly added object\. The example uses this version ID in the delete request\.

1. Deletes the object version by specifying both the object key name and a version ID\. If there are no other versions of that object, Amazon S3 deletes the object entirely\. Otherwise, Amazon S3 only deletes the specified version\.
**Note**  
You can get the version IDs of an object by sending a `ListVersions` request\.

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.BucketVersioningConfiguration;
import com.amazonaws.services.s3.model.DeleteVersionRequest;
import com.amazonaws.services.s3.model.PutObjectResult;

import java.io.IOException;

public class DeleteObjectVersionEnabledBucket {

    public static void main(String[] args) throws IOException {
        Regions clientRegion = Regions.DEFAULT_REGION;
        String bucketName = "*** Bucket name ***";
        String keyName = "*** Key name ****";

        try {
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .withCredentials(new ProfileCredentialsProvider())
                    .withRegion(clientRegion)
                    .build();

            // Check to ensure that the bucket is versioning-enabled.
            String bucketVersionStatus = s3Client.getBucketVersioningConfiguration(bucketName).getStatus();
            if (!bucketVersionStatus.equals(BucketVersioningConfiguration.ENABLED)) {
                System.out.printf("Bucket %s is not versioning-enabled.", bucketName);
            } else {
                // Add an object.
                PutObjectResult putResult = s3Client.putObject(bucketName, keyName, "Sample content for deletion example.");
                System.out.printf("Object %s added to bucket %s\n", keyName, bucketName);

                // Delete the version of the object that we just created.
                System.out.println("Deleting versioned object " + keyName);
                s3Client.deleteVersion(new DeleteVersionRequest(bucketName, keyName, putResult.getVersionId()));
                System.out.printf("Object %s, version %s deleted\n", keyName, putResult.getVersionId());
            }
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

The following examples show how to delete an object from both versioned and non\-versioned buckets\. For more information about S3 Versioning, see [Using versioning in S3 buckets](Versioning.md)\. 

**Example Deleting an object from a non\-versioned bucket**  
The following C\# example deletes an object from a non\-versioned bucket\. The example assumes that the objects don't have version IDs, so you don't specify version IDs\. You specify only the object key\.   
For information about how to create and test a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\.   

```
using Amazon;
using Amazon.S3;
using Amazon.S3.Model;
using System;
using System.Threading.Tasks;

namespace Amazon.DocSamples.S3
{
    class DeleteObjectNonVersionedBucketTest
    {
        private const string bucketName = "*** bucket name ***"; 
        private const string keyName = "*** object key ***";
        // Specify your bucket region (an example region is shown).
        private static readonly RegionEndpoint bucketRegion = RegionEndpoint.USWest2;
        private static IAmazonS3 client;

        public static void Main()
        {
            client = new AmazonS3Client(bucketRegion);
            DeleteObjectNonVersionedBucketAsync().Wait();
        }
        private static async Task DeleteObjectNonVersionedBucketAsync()
        {
            try
            {
                var deleteObjectRequest = new DeleteObjectRequest
                {
                    BucketName = bucketName,
                    Key = keyName
                };

                Console.WriteLine("Deleting an object");
                await client.DeleteObjectAsync(deleteObjectRequest);
            }
            catch (AmazonS3Exception e)
            {
                Console.WriteLine("Error encountered on server. Message:'{0}' when deleting an object", e.Message);
            }
            catch (Exception e)
            {
                Console.WriteLine("Unknown encountered on server. Message:'{0}' when deleting an object", e.Message);
            }
        }
    }
}
```

**Example Deleting an object from a versioned bucket**  
The following C\# example deletes an object from a versioned bucket\. It deletes a specific version of the object by specifying the object key name and version ID\.   
The code performs the following tasks:  

1. Enables S3 Versioning on a bucket that you specify \(if S3 Versioning is already enabled, this has no effect\)\.

1. Adds a sample object to the bucket\. In response, Amazon S3 returns the version ID of the newly added object\. The example uses this version ID in the delete request\.

1. Deletes the sample object by specifying both the object key name and a version ID\.
**Note**  
You can also get the version ID of an object by sending a `ListVersions` request\.  

   ```
   var listResponse = client.ListVersions(new ListVersionsRequest { BucketName = bucketName, Prefix = keyName }); 
   ```
For information about how to create and test a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\.   

```
using Amazon;
using Amazon.S3;
using Amazon.S3.Model;
using System;
using System.Threading.Tasks;

namespace Amazon.DocSamples.S3
{
    class DeleteObjectVersion
    {
        private const string bucketName = "*** versioning-enabled bucket name ***";
        private const string keyName = "*** Object Key Name ***";
        // Specify your bucket region (an example region is shown).
        private static readonly RegionEndpoint bucketRegion = RegionEndpoint.USWest2;
        private static IAmazonS3 client;

        public static void Main()
        {
            client = new AmazonS3Client(bucketRegion);
            CreateAndDeleteObjectVersionAsync().Wait();
        }

        private static async Task CreateAndDeleteObjectVersionAsync()
        {
            try
            {
                // Add a sample object. 
                string versionID = await PutAnObject(keyName);

                // Delete the object by specifying an object key and a version ID.
                DeleteObjectRequest request = new DeleteObjectRequest
                {
                    BucketName = bucketName,
                    Key = keyName,
                    VersionId = versionID
                };
                Console.WriteLine("Deleting an object");
                await client.DeleteObjectAsync(request);
            }
            catch (AmazonS3Exception e)
            {
                Console.WriteLine("Error encountered on server. Message:'{0}' when deleting an object", e.Message);
            }
            catch (Exception e)
            {
                Console.WriteLine("Unknown encountered on server. Message:'{0}' when deleting an object", e.Message);
            }
        }

        static async Task<string> PutAnObject(string objectKey)
        {
            PutObjectRequest request = new PutObjectRequest
            {
                BucketName = bucketName,
                Key = objectKey,
                ContentBody = "This is the content body!"
            };
            PutObjectResponse response = await client.PutObjectAsync(request);
            return response.VersionId;
        }
    }
}
```

------
#### [ PHP ]

This example shows how to use classes from version 3 of the AWS SDK for PHP to delete an object from a non\-versioned bucket\. For information about deleting an object from a versioned bucket, see [Using the REST API](DeletingAnObjectsUsingREST.md)\. 

This example assumes that you are already following the instructions for [Using the AWS SDK for PHP and Running PHP Examples](UsingTheMPphpAPI.md) and have the AWS SDK for PHP properly installed\. For information about running the PHP examples in this guide, see [Running PHP Examples](UsingTheMPphpAPI.md#running-php-samples)\.

The following PHP example deletes an object from a bucket\. Because this example shows how to delete objects from non\-versioned buckets, it provides only the bucket name and object key \(not a version ID\) in the delete request\. 

```
<?php

require 'vendor/autoload.php';

use Aws\S3\S3Client;
use Aws\S3\Exception\S3Exception;

$bucket = '*** Your Bucket Name ***';
$keyname = '*** Your Object Key ***';

$s3 = new S3Client([
    'version' => 'latest',
    'region'  => 'us-east-1'
]);

// 1. Delete the object from the bucket.
try
{
    echo 'Attempting to delete ' . $keyname . '...' . PHP_EOL;

    $result = $s3->deleteObject([
        'Bucket' => $bucket,
        'Key'    => $keyname
    ]);

    if ($result['DeleteMarker'])
    {
        echo $keyname . ' was deleted or does not exist.' . PHP_EOL;
    } else {
        exit('Error: ' . $keyname . ' was not deleted.' . PHP_EOL);
    }
}
catch (S3Exception $e) {
    exit('Error: ' . $e->getAwsErrorMessage() . PHP_EOL);
}

// 2. Check to see if the object was deleted.
try
{
    echo 'Checking to see if ' . $keyname . ' still exists...' . PHP_EOL;

    $result = $s3->getObject([
        'Bucket' => $bucket,
        'Key'    => $keyname
    ]);

    echo 'Error: ' . $keyname . ' still exists.';
}
catch (S3Exception $e) {
    exit($e->getAwsErrorMessage());
}
```

------

## Using the AWS CLI<a name="delete-object-cli"></a>



To delete one object per request, use the `DELETE` API\. For more information, see [DELETE Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectDELETE.html)\. For more information about using the CLI to delete an object, see [delete\-object](https://awscli.amazonaws.com/v2/documentation/api/2.0.34/reference/s3api/delete-object.html)\.