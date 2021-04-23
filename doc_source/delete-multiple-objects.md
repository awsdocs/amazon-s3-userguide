# Deleting multiple objects<a name="delete-multiple-objects"></a>

Because all objects in your S3 bucket incur storage costs, you should delete objects that you no longer need\. For example, if you are collecting log files, it's a good idea to delete them when they're no longer needed\. You can set up a lifecycle rule to automatically delete objects such as log files\. For more information, see [Setting lifecycle configuration on a bucket](how-to-set-lifecycle-configuration-intro.md)\.

For information about Amazon S3 features and pricing, see [Amazon S3 pricing](http://aws.amazon.com/s3/pricing)\.

You can use the Amazon S3 console or the Multi\-Object Delete API to delete multiple objects simultaneously from an S3 bucket\.

## Using the S3 console<a name="delete-objects-console"></a>

Follow these steps to use the Amazon S3 console to delete multiple objects from a bucket\.

**To delete objects**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Navigate to the Amazon S3 bucket or folder that contains the objects that you want to delete\.

1. Select the check box to the left of the names of the objects that you want to delete\.

1. Choose **Actions** and choose **Delete** from the list of options that appears\.

   Alternatively, choose **Delete** from the options in the upper right\. 

1. Enter **delete** if asked to confirm that you want to delete these objects\.

1. Choose **Delete objects** in the bottom right and Amazon S3 deletes the specified objects\.

**Warning**  
Deleting the specified objects cannot be undone\.
This action deletes all specified objects\. When deleting folders, wait for the delete action to finish before adding new objects to the folder\. Otherwise, new objects might be deleted as well\.
Deleting the specified objects cannot be undone\.

## Using the AWS SDKs<a name="DeletingMultipleObjects"></a>

Amazon S3 provides the Multi\-Object Delete API , which you can use to delete multiple objects in a single request\. The API supports two modes for the response: *verbose* and *quiet*\. By default, the operation uses verbose mode\. In verbose mode, the response includes the result of the deletion of each key that is specified in your request\. In quiet mode, the response includes only keys for which the delete operation encountered an error\. If all keys are successfully deleted when you're using quiet mode, Amazon S3 returns an empty response\. For more information, see [Delete \- Multi\-Object Delete](https://docs.aws.amazon.com/AmazonS3/latest/API/multiobjectdeleteapi.html)\.

To learn more about object deletion, see [Deleting Amazon S3 objects](DeletingObjects.md)\. 

------
#### [ Java ]

The AWS SDK for Java provides the `AmazonS3Client.deleteObjects()` method for deleting multiple objects\. For each object that you want to delete, you specify the key name\. If the bucket is versioning\-enabled, you have the following options:
+ Specify only the object's key name\. Amazon S3 adds a delete marker to the object\.
+ Specify both the object's key name and a version ID to be deleted\. Amazon S3 deletes the specified version of the object\.

**Example**  
The following example uses the Multi\-Object Delete API to delete objects from a bucket that is not version\-enabled\. The example uploads sample objects to the bucket and then uses the `AmazonS3Client.deleteObjects()` method to delete the objects in a single request\. In the `DeleteObjectsRequest`, the example specifies only the object key names because the objects do not have version IDs\.   
For instructions on creating and testing a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\.   

```
 
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.DeleteObjectsRequest;
import com.amazonaws.services.s3.model.DeleteObjectsRequest.KeyVersion;
import com.amazonaws.services.s3.model.DeleteObjectsResult;

import java.io.IOException;
import java.util.ArrayList;

public class DeleteMultipleObjectsNonVersionedBucket {

    public static void main(String[] args) throws IOException {
        Regions clientRegion = Regions.DEFAULT_REGION;
        String bucketName = "*** Bucket name ***";

        try {
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .withRegion(clientRegion)
                    .build();

            // Upload three sample objects.
            ArrayList<KeyVersion> keys = new ArrayList<KeyVersion>();
            for (int i = 0; i < 3; i++) {
                String keyName = "delete object example " + i;
                s3Client.putObject(bucketName, keyName, "Object number " + i + " to be deleted.");
                keys.add(new KeyVersion(keyName));
            }
            System.out.println(keys.size() + " objects successfully created.");

            // Delete the sample objects.
            DeleteObjectsRequest multiObjectDeleteRequest = new DeleteObjectsRequest(bucketName)
                    .withKeys(keys)
                    .withQuiet(false);

            // Verify that the objects were deleted successfully.
            DeleteObjectsResult delObjRes = s3Client.deleteObjects(multiObjectDeleteRequest);
            int successfulDeletes = delObjRes.getDeletedObjects().size();
            System.out.println(successfulDeletes + " objects successfully deleted.");
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

**Example**  
The following example uses the Multi\-Object Delete API to delete objects from a version\-enabled bucket\. It does the following:   

1. Creates sample objects and then deletes them, specifying the key name and version ID for each object to delete\. The operation deletes only the specified object versions\.

1. Creates sample objects and then deletes them by specifying only the key names\. Because the example doesn't specify version IDs, the operation adds a delete marker to each object, without deleting any specific object versions\. After the delete markers are added, these objects will not appear in the AWS Management Console\.

1. Removes the delete markers by specifying the object keys and version IDs of the delete markers\. The operation deletes the delete markers, which results in the objects reappearing in the AWS Management Console\.

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.BucketVersioningConfiguration;
import com.amazonaws.services.s3.model.DeleteObjectsRequest;
import com.amazonaws.services.s3.model.DeleteObjectsRequest.KeyVersion;
import com.amazonaws.services.s3.model.DeleteObjectsResult;
import com.amazonaws.services.s3.model.DeleteObjectsResult.DeletedObject;
import com.amazonaws.services.s3.model.PutObjectResult;

import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

public class DeleteMultipleObjectsVersionEnabledBucket {
    private static AmazonS3 S3_CLIENT;
    private static String VERSIONED_BUCKET_NAME;

    public static void main(String[] args) throws IOException {
        Regions clientRegion = Regions.DEFAULT_REGION;
        VERSIONED_BUCKET_NAME = "*** Bucket name ***";

        try {
            S3_CLIENT = AmazonS3ClientBuilder.standard()
                    .withCredentials(new ProfileCredentialsProvider())
                    .withRegion(clientRegion)
                    .build();

            // Check to make sure that the bucket is versioning-enabled.
            String bucketVersionStatus = S3_CLIENT.getBucketVersioningConfiguration(VERSIONED_BUCKET_NAME).getStatus();
            if (!bucketVersionStatus.equals(BucketVersioningConfiguration.ENABLED)) {
                System.out.printf("Bucket %s is not versioning-enabled.", VERSIONED_BUCKET_NAME);
            } else {
                // Upload and delete sample objects, using specific object versions.
                uploadAndDeleteObjectsWithVersions();

                // Upload and delete sample objects without specifying version IDs.
                // Amazon S3 creates a delete marker for each object rather than deleting
                // specific versions.
                DeleteObjectsResult unversionedDeleteResult = uploadAndDeleteObjectsWithoutVersions();

                // Remove the delete markers placed on objects in the non-versioned create/delete method.
                multiObjectVersionedDeleteRemoveDeleteMarkers(unversionedDeleteResult);
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

    private static void uploadAndDeleteObjectsWithVersions() {
        System.out.println("Uploading and deleting objects with versions specified.");

        // Upload three sample objects.
        ArrayList<KeyVersion> keys = new ArrayList<KeyVersion>();
        for (int i = 0; i < 3; i++) {
            String keyName = "delete object without version ID example " + i;
            PutObjectResult putResult = S3_CLIENT.putObject(VERSIONED_BUCKET_NAME, keyName,
                    "Object number " + i + " to be deleted.");
            // Gather the new object keys with version IDs.
            keys.add(new KeyVersion(keyName, putResult.getVersionId()));
        }

        // Delete the specified versions of the sample objects.
        DeleteObjectsRequest multiObjectDeleteRequest = new DeleteObjectsRequest(VERSIONED_BUCKET_NAME)
                .withKeys(keys)
                .withQuiet(false);

        // Verify that the object versions were successfully deleted.
        DeleteObjectsResult delObjRes = S3_CLIENT.deleteObjects(multiObjectDeleteRequest);
        int successfulDeletes = delObjRes.getDeletedObjects().size();
        System.out.println(successfulDeletes + " objects successfully deleted");
    }

    private static DeleteObjectsResult uploadAndDeleteObjectsWithoutVersions() {
        System.out.println("Uploading and deleting objects with no versions specified.");

        // Upload three sample objects.
        ArrayList<KeyVersion> keys = new ArrayList<KeyVersion>();
        for (int i = 0; i < 3; i++) {
            String keyName = "delete object with version ID example " + i;
            S3_CLIENT.putObject(VERSIONED_BUCKET_NAME, keyName, "Object number " + i + " to be deleted.");
            // Gather the new object keys without version IDs.
            keys.add(new KeyVersion(keyName));
        }

        // Delete the sample objects without specifying versions.
        DeleteObjectsRequest multiObjectDeleteRequest = new DeleteObjectsRequest(VERSIONED_BUCKET_NAME).withKeys(keys)
                .withQuiet(false);

        // Verify that delete markers were successfully added to the objects.
        DeleteObjectsResult delObjRes = S3_CLIENT.deleteObjects(multiObjectDeleteRequest);
        int successfulDeletes = delObjRes.getDeletedObjects().size();
        System.out.println(successfulDeletes + " objects successfully marked for deletion without versions.");
        return delObjRes;
    }

    private static void multiObjectVersionedDeleteRemoveDeleteMarkers(DeleteObjectsResult response) {
        List<KeyVersion> keyList = new ArrayList<KeyVersion>();
        for (DeletedObject deletedObject : response.getDeletedObjects()) {
            // Note that the specified version ID is the version ID for the delete marker.
            keyList.add(new KeyVersion(deletedObject.getKey(), deletedObject.getDeleteMarkerVersionId()));
        }
        // Create a request to delete the delete markers.
        DeleteObjectsRequest deleteRequest = new DeleteObjectsRequest(VERSIONED_BUCKET_NAME).withKeys(keyList);

        // Delete the delete markers, leaving the objects intact in the bucket.
        DeleteObjectsResult delObjRes = S3_CLIENT.deleteObjects(deleteRequest);
        int successfulDeletes = delObjRes.getDeletedObjects().size();
        System.out.println(successfulDeletes + " delete markers successfully deleted");
    }
}
```

------
#### [ \.NET ]

The AWS SDK for \.NET provides a convenient method for deleting multiple objects: `DeleteObjects`\. For each object that you want to delete, you specify the key name and the version of the object\. If the bucket is not versioning\-enabled, you specify `null` for the version ID\. If an exception occurs, review the `DeleteObjectsException` response to determine which objects were not deleted and why\. 

**Example Deleting multiple objects from a non\-versioning bucket**  
The following C\# example uses the multi\-object delete API to delete objects from a bucket that is not version\-enabled\. The example uploads the sample objects to the bucket, and then uses the `DeleteObjects` method to delete the objects in a single request\. In the `DeleteObjectsRequest`, the example specifies only the object key names because the version IDs are null\.  
For information about creating and testing a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\.  

```
using Amazon;
using Amazon.S3;
using Amazon.S3.Model;
using System;
using System.Collections.Generic;
using System.Threading.Tasks;

namespace Amazon.DocSamples.S3
{
    class DeleteMultipleObjectsNonVersionedBucketTest
    {
        private const string bucketName = "*** versioning-enabled bucket name ***";
        // Specify your bucket region (an example region is shown).
        private static readonly RegionEndpoint bucketRegion = RegionEndpoint.USWest2;
        private static IAmazonS3 s3Client;

        public static void Main()
        {
            s3Client = new AmazonS3Client(bucketRegion);
            MultiObjectDeleteAsync().Wait();
        }

        static async Task MultiObjectDeleteAsync()
        {
            // Create sample objects (for subsequent deletion).
            var keysAndVersions = await PutObjectsAsync(3);

            // a. multi-object delete by specifying the key names and version IDs.
            DeleteObjectsRequest multiObjectDeleteRequest = new DeleteObjectsRequest
            {
                BucketName = bucketName,
                Objects = keysAndVersions // This includes the object keys and null version IDs.
            };
            // You can add specific object key to the delete request using the .AddKey.
            // multiObjectDeleteRequest.AddKey("TickerReference.csv", null);
            try
            {
                DeleteObjectsResponse response = await s3Client.DeleteObjectsAsync(multiObjectDeleteRequest);
                Console.WriteLine("Successfully deleted all the {0} items", response.DeletedObjects.Count);
            }
            catch (DeleteObjectsException e)
            {
                PrintDeletionErrorStatus(e);
            }
        }

        private static void PrintDeletionErrorStatus(DeleteObjectsException e)
        {
            // var errorResponse = e.ErrorResponse;
            DeleteObjectsResponse errorResponse = e.Response;
            Console.WriteLine("x {0}", errorResponse.DeletedObjects.Count);

            Console.WriteLine("No. of objects successfully deleted = {0}", errorResponse.DeletedObjects.Count);
            Console.WriteLine("No. of objects failed to delete = {0}", errorResponse.DeleteErrors.Count);

            Console.WriteLine("Printing error data...");
            foreach (DeleteError deleteError in errorResponse.DeleteErrors)
            {
                Console.WriteLine("Object Key: {0}\t{1}\t{2}", deleteError.Key, deleteError.Code, deleteError.Message);
            }
        }

        static async Task<List<KeyVersion>> PutObjectsAsync(int number)
        {
            List<KeyVersion> keys = new List<KeyVersion>();
            for (int i = 0; i < number; i++)
            {
                string key = "ExampleObject-" + new System.Random().Next();
                PutObjectRequest request = new PutObjectRequest
                {
                    BucketName = bucketName,
                    Key = key,
                    ContentBody = "This is the content body!",
                };

                PutObjectResponse response = await s3Client.PutObjectAsync(request);
                KeyVersion keyVersion = new KeyVersion
                {
                    Key = key,
                    // For non-versioned bucket operations, we only need object key.
                    // VersionId = response.VersionId
                };
                keys.Add(keyVersion);
            }
            return keys;
        }
    }
}
```

**Example Multi\-object deletion for a version\-enabled bucket**  
The following C\# example uses the multi\-object delete API to delete objects from a version\-enabled bucket\. The example performs the following actions:  

1. Creates sample objects and deletes them by specifying the key name and version ID for each object\. The operation deletes specific versions of the objects\.

1. Creates sample objects and deletes them by specifying only the key names\. Because the example doesn't specify version IDs, the operation only adds delete markers\. It doesn't delete any specific versions of the objects\. After deletion, these objects don't appear in the Amazon S3 console\.

1. Deletes the delete markers by specifying the object keys and version IDs of the delete markers\. When the operation deletes the delete markers, the objects reappear in the console\.
For information about creating and testing a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\.  

```
using Amazon;
using Amazon.S3;
using Amazon.S3.Model;
using System;
using System.Collections.Generic;
using System.Threading.Tasks;

namespace Amazon.DocSamples.S3
{
    class DeleteMultipleObjVersionedBucketTest
    {
        private const string bucketName = "*** versioning-enabled bucket name ***"; 
       // Specify your bucket region (an example region is shown).
        private static readonly RegionEndpoint bucketRegion = RegionEndpoint.USWest2;
        private static IAmazonS3 s3Client;

        public static void Main()
        {
            s3Client = new AmazonS3Client(bucketRegion);
            DeleteMultipleObjectsFromVersionedBucketAsync().Wait();
        }

        private static async Task DeleteMultipleObjectsFromVersionedBucketAsync()
        {

            // Delete objects (specifying object version in the request).
            await DeleteObjectVersionsAsync();

            // Delete objects (without specifying object version in the request). 
            var deletedObjects = await DeleteObjectsAsync();

            // Additional exercise - remove the delete markers S3 returned in the preceding response. 
            // This results in the objects reappearing in the bucket (you can 
            // verify the appearance/disappearance of objects in the console).
            await RemoveDeleteMarkersAsync(deletedObjects);
        }

        private static async Task<List<DeletedObject>> DeleteObjectsAsync()
        {
            // Upload the sample objects.
            var keysAndVersions2 = await PutObjectsAsync(3);

            // Delete objects using only keys. Amazon S3 creates a delete marker and 
            // returns its version ID in the response.
            List<DeletedObject> deletedObjects = await NonVersionedDeleteAsync(keysAndVersions2);
            return deletedObjects;
        }

        private static async Task DeleteObjectVersionsAsync()
        {
            // Upload the sample objects.
            var keysAndVersions1 = await PutObjectsAsync(3);

            // Delete the specific object versions.
            await VersionedDeleteAsync(keysAndVersions1);
        }

        private static void PrintDeletionReport(DeleteObjectsException e)
        {
            var errorResponse = e.Response;
            Console.WriteLine("No. of objects successfully deleted = {0}", errorResponse.DeletedObjects.Count);
            Console.WriteLine("No. of objects failed to delete = {0}", errorResponse.DeleteErrors.Count);
            Console.WriteLine("Printing error data...");
            foreach (var deleteError in errorResponse.DeleteErrors)
            {
                Console.WriteLine("Object Key: {0}\t{1}\t{2}", deleteError.Key, deleteError.Code, deleteError.Message);
            }
        }

        static async Task VersionedDeleteAsync(List<KeyVersion> keys)
        {
            // a. Perform a multi-object delete by specifying the key names and version IDs.
            var multiObjectDeleteRequest = new DeleteObjectsRequest
            {
                BucketName = bucketName,
                Objects = keys // This includes the object keys and specific version IDs.
            };
            try
            {
                Console.WriteLine("Executing VersionedDelete...");
                DeleteObjectsResponse response = await s3Client.DeleteObjectsAsync(multiObjectDeleteRequest);
                Console.WriteLine("Successfully deleted all the {0} items", response.DeletedObjects.Count);
            }
            catch (DeleteObjectsException e)
            {
                PrintDeletionReport(e);
            }
        }

        static async Task<List<DeletedObject>> NonVersionedDeleteAsync(List<KeyVersion> keys)
        {
            // Create a request that includes only the object key names.
            DeleteObjectsRequest multiObjectDeleteRequest = new DeleteObjectsRequest();
            multiObjectDeleteRequest.BucketName = bucketName;

            foreach (var key in keys)
            {
                multiObjectDeleteRequest.AddKey(key.Key);
            }
            // Execute DeleteObjects - Amazon S3 add delete marker for each object
            // deletion. The objects disappear from your bucket. 
            // You can verify that using the Amazon S3 console.
            DeleteObjectsResponse response;
            try
            {
                Console.WriteLine("Executing NonVersionedDelete...");
                response = await s3Client.DeleteObjectsAsync(multiObjectDeleteRequest);
                Console.WriteLine("Successfully deleted all the {0} items", response.DeletedObjects.Count);
            }
            catch (DeleteObjectsException e)
            {
                PrintDeletionReport(e);
                throw; // Some deletes failed. Investigate before continuing.
            }
            // This response contains the DeletedObjects list which we use to delete the delete markers.
            return response.DeletedObjects;
        }

        private static async Task RemoveDeleteMarkersAsync(List<DeletedObject> deletedObjects)
        {
            var keyVersionList = new List<KeyVersion>();

            foreach (var deletedObject in deletedObjects)
            {
                KeyVersion keyVersion = new KeyVersion
                {
                    Key = deletedObject.Key,
                    VersionId = deletedObject.DeleteMarkerVersionId
                };
                keyVersionList.Add(keyVersion);
            }
            // Create another request to delete the delete markers.
            var multiObjectDeleteRequest = new DeleteObjectsRequest
            {
                BucketName = bucketName,
                Objects = keyVersionList
            };

            // Now, delete the delete marker to bring your objects back to the bucket.
            try
            {
                Console.WriteLine("Removing the delete markers .....");
                var deleteObjectResponse = await s3Client.DeleteObjectsAsync(multiObjectDeleteRequest);
                Console.WriteLine("Successfully deleted all the {0} delete markers",
                                            deleteObjectResponse.DeletedObjects.Count);
            }
            catch (DeleteObjectsException e)
            {
                PrintDeletionReport(e);
            }
        }

        static async Task<List<KeyVersion>> PutObjectsAsync(int number)
        {
            var keys = new List<KeyVersion>();

            for (var i = 0; i < number; i++)
            {
                string key = "ObjectToDelete-" + new System.Random().Next();
                PutObjectRequest request = new PutObjectRequest
                {
                    BucketName = bucketName,
                    Key = key,
                    ContentBody = "This is the content body!",

                };

                var response = await s3Client.PutObjectAsync(request);
                KeyVersion keyVersion = new KeyVersion
                {
                    Key = key,
                    VersionId = response.VersionId
                };

                keys.Add(keyVersion);
            }
            return keys;
        }
    }
}
```

------
#### [ PHP ]

These examples show how to use classes from version 3 of the AWS SDK for PHP to delete multiple objects from versioned and non\-versioned Amazon S3 buckets\. For more information about versioning, see [Using versioning in S3 buckets](Versioning.md)\.

 The examples assume that you are already following the instructions for [Using the AWS SDK for PHP and Running PHP Examples](UsingTheMPphpAPI.md) and have the AWS SDK for PHP properly installed\.

**Example Deleting multiple objects from a non\-versioned bucket**  
The following PHP example uses the `deleteObjects()` method to delete multiple objects from a bucket that is not version\-enabled\.  
 For information about running the PHP examples in this guide, see [Running PHP Examples](UsingTheMPphpAPI.md#running-php-samples)\.   

```
<?php

require 'vendor/autoload.php';

use Aws\S3\S3Client;

$bucket = '*** Your Bucket Name ***';

$s3 = new S3Client([
    'version' => 'latest',
    'region'  => 'us-east-1'
]);

// 1. Create a few objects.
for ($i = 1; $i <= 3; $i++) {
    $s3->putObject([
        'Bucket' => $bucket,
        'Key'    => "key{$i}",
        'Body'   => "content {$i}",
    ]);
}

// 2. List the objects and get the keys.
$keys = $s3->listObjects([
    'Bucket' => $bucket
]); 

// 3. Delete the objects.
foreach ($keys['Contents'] as $key)
{
    $s3->deleteObjects([
        'Bucket'  => $bucket,
        'Delete' => [
            'Objects' => [
                [
                    'Key' => $key['Key']
                ]
            ]
        ]
    ]);
}
```

**Example Deleting multiple objects from a version\-enabled bucket**  
The following PHP example uses the `deleteObjects()` method to delete multiple objects from a version\-enabled bucket\.  
 For information about running the PHP examples in this guide, see [Running PHP Examples](UsingTheMPphpAPI.md#running-php-samples)\.   

```
<?php

require 'vendor/autoload.php';

use Aws\S3\S3Client;

$bucket = '*** Your Bucket Name ***';
$keyname = '*** Your Object Key ***';

$s3 = new S3Client([
    'version' => 'latest',
    'region'  => 'us-east-1'
]);

// 1. Enable object versioning for the bucket.
$s3->putBucketVersioning([
    'Bucket' => $bucket,
    'VersioningConfiguration' => [ 
        'Status' => 'Enabled'
    ]
]);

// 2. Create a few versions of an object.
for ($i = 1; $i <= 3; $i++) {
    $s3->putObject([
        'Bucket' => $bucket,
        'Key'    => $keyname,
        'Body'   => "content {$i}",
    ]);
}

// 3. List the objects versions and get the keys and version IDs.
$versions = $s3->listObjectVersions(['Bucket' => $bucket]);

// 4. Delete the object versions.
$deletedResults = 'The following objects were deleted successfully:' . PHP_EOL;
$deleted = false;
$errorResults = 'The following objects could not be deleted:' . PHP_EOL;
$errors = false;

foreach ($versions['Versions'] as $version)
{
    $result = $s3->deleteObjects([
        'Bucket'  => $bucket,
        'Delete' => [
            'Objects' => [
                [
                    'Key' => $version['Key'],
                    'VersionId' => $version['VersionId']
                ]
            ]
        ]
    ]);

    if (isset($result['Deleted']))
    {
        $deleted = true;

        $deletedResults .= "Key: {$result['Deleted'][0]['Key']}, " . 
            "VersionId: {$result['Deleted'][0]['VersionId']}" . PHP_EOL;
    }

    if (isset($result['Errors']))
    {
        $errors = true;

        $errorResults .= "Key: {$result['Errors'][0]['Key']}, " . 
            "VersionId: {$result['Errors'][0]['VersionId']}, " .
            "Message: {$result['Errors'][0]['Message']}" . PHP_EOL;
    }
}

if ($deleted)
{
    echo $deletedResults;
}

if ($errors)
{
    echo $errorResults;
}

// 5. Suspend object versioning for the bucket.
$s3->putBucketVersioning([
    'Bucket' => $bucket,
    'VersioningConfiguration' => [ 
        'Status' => 'Suspended'
    ]
]);
```

------