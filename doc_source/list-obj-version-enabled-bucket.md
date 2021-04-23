# Listing objects in a versioning\-enabled bucket<a name="list-obj-version-enabled-bucket"></a>

This section provides examples of listing object versions from a versioning\-enabled bucket\. Amazon S3 stores object version information in the *versions* subresource that is associated with the bucket\. For more information, see [Bucket configuration options](UsingBucket.md#bucket-config-options-intro)\.

## Using the S3 console<a name="view-object-versions"></a>

Follow these steps to use the Amazon S3 console to see the different versions of an object\.

**To see multiple versions of an object**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that contains the object\.

1. To see a list of the versions of the objects in the bucket, choose the **List versions** switch\. 

   For each object version, the console shows a unique version ID, the date and time the object version was created, and other properties\. \(Objects stored in your bucket before you set the versioning state have a version ID of **null**\.\)

   To list the objects without the versions, choose the **List versions** switch\.

You also can view, download, and delete object versions in the object overview pane on the console\. For more information, see [Viewing an object overview in the Amazon S3 console](view-object-overview.md)\.

**Important**  
You can undelete an object only if it was deleted as the latest \(current\) version\. You can't undelete a previous version of an object that was deleted\. For more information, see [Using versioning in S3 buckets](Versioning.md)\.

## Using the AWS SDKs<a name="list-obj-version-enabled-bucket-sdk-examples"></a>

The examples in this section show how to retrieve an object listing from a versioning\-enabled bucket\. Each request returns up to 1,000 versions, unless you specify a lower number\. If the bucket contains more versions than this limit, you send a series of requests to retrieve the list of all versions\. This process of returning results in "pages" is called *pagination*\.

To show how pagination works, the examples limit each response to two object versions\. After retrieving the first page of results, each example checks to determine whether the version list was truncated\. If it was, the example continues retrieving pages until all versions have been retrieved\. 

**Note**  
The following examples also work with a bucket that isn't versioning\-enabled, or for objects that don't have individual versions\. In those cases, Amazon S3 returns the object listing with a version ID of `null`\.

 For information about using other AWS SDKs, see the [AWS Developer Center](https://aws.amazon.com/code/)\. 

------
#### [ Java ]

For instructions on creating and testing a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\. 

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.ListVersionsRequest;
import com.amazonaws.services.s3.model.S3VersionSummary;
import com.amazonaws.services.s3.model.VersionListing;

public class ListKeysVersioningEnabledBucket {

    public static void main(String[] args) {
        Regions clientRegion = Regions.DEFAULT_REGION;
        String bucketName = "*** Bucket name ***";

        try {
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .withCredentials(new ProfileCredentialsProvider())
                    .withRegion(clientRegion)
                    .build();

            // Retrieve the list of versions. If the bucket contains more versions
            // than the specified maximum number of results, Amazon S3 returns
            // one page of results per request.
            ListVersionsRequest request = new ListVersionsRequest()
                    .withBucketName(bucketName)
                    .withMaxResults(2);
            VersionListing versionListing = s3Client.listVersions(request);
            int numVersions = 0, numPages = 0;
            while (true) {
                numPages++;
                for (S3VersionSummary objectSummary :
                        versionListing.getVersionSummaries()) {
                    System.out.printf("Retrieved object %s, version %s\n",
                            objectSummary.getKey(),
                            objectSummary.getVersionId());
                    numVersions++;
                }
                // Check whether there are more pages of versions to retrieve. If
                // there are, retrieve them. Otherwise, exit the loop.
                if (versionListing.isTruncated()) {
                    versionListing = s3Client.listNextBatchOfVersions(versionListing);
                } else {
                    break;
                }
            }
            System.out.println(numVersions + " object versions retrieved in " + numPages + " pages");
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

For information about how to create and test a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\. 

```
using Amazon;
using Amazon.S3;
using Amazon.S3.Model;
using System;
using System.Threading.Tasks;

namespace Amazon.DocSamples.S3
{
    class ListObjectsVersioningEnabledBucketTest
    {
        static string bucketName = "*** bucket name ***";
        // Specify your bucket region (an example region is shown).
        private static readonly RegionEndpoint bucketRegion = RegionEndpoint.USWest2;
        private static IAmazonS3 s3Client;

        public static void Main(string[] args)
        {
            s3Client = new AmazonS3Client(bucketRegion);
            GetObjectListWithAllVersionsAsync().Wait();
        }

        static async Task GetObjectListWithAllVersionsAsync()
        {
            try
            {
                ListVersionsRequest request = new ListVersionsRequest()
                {
                    BucketName = bucketName,
                    // You can optionally specify key name prefix in the request
                    // if you want list of object versions of a specific object.

                    // For this example we limit response to return list of 2 versions.
                    MaxKeys = 2
                };
                do
                {
                    ListVersionsResponse response = await s3Client.ListVersionsAsync(request); 
                    // Process response.
                    foreach (S3ObjectVersion entry in response.Versions)
                    {
                        Console.WriteLine("key = {0} size = {1}",
                            entry.Key, entry.Size);
                    }

                    // If response is truncated, set the marker to get the next 
                    // set of keys.
                    if (response.IsTruncated)
                    {
                        request.KeyMarker = response.NextKeyMarker;
                        request.VersionIdMarker = response.NextVersionIdMarker;
                    }
                    else
                    {
                        request = null;
                    }
                } while (request != null);
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

## Using the REST API<a name="ListingtheObjectsinaVersioningEnabledBucket"></a>

**Example — Listing all object versions in a bucket**  
To list all the versions of all the objects in a bucket, you use the `versions` subresource in a `GET Bucket` request\. Amazon S3 can retrieve a maximum of 1,000 objects, and each object version counts fully as an object\. Therefore, if a bucket contains two keys \(for example, `photo.gif` and `picture.jpg`\), and the first key has 990 versions and the second key has 400 versions, a single request would retrieve all 990 versions of `photo.gif` and only the most recent 10 versions of `picture.jpg`\.  
Amazon S3 returns object versions in the order in which they were stored, with the most recently stored returned first\.  
In a `GET Bucket` request, include the `versions` subresource\.  

```
1. GET /?versions HTTP/1.1
2. Host: bucketName.s3.amazonaws.com
3. Date: Wed, 28 Oct 2009 22:32:00 +0000
4. Authorization: AWS AKIAIOSFODNN7EXAMPLE:0RQf4/cRonhpaBX5sCYVf1bNRuU=
```

**Example — Retrieving all versions of a key**  
 To retrieve a subset of object versions, you use the request parameters for GET Bucket\. For more information, see [GET Bucket](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGET.html)\.   

1. Set the `prefix` parameter to the key of the object that you want to retrieve\.

1. Send a `GET Bucket` request using the `versions` subresource and `prefix`\.

   `GET /?versions&prefix=objectName HTTP/1.1`

**Example — Retrieving objects using a prefix**  
The following example retrieves objects whose key is or begins with `myObject`\.  

```
1. GET /?versions&prefix=myObject HTTP/1.1
2. Host: bucket.s3.amazonaws.com
3. Date: Wed, 28 Oct 2009 22:32:00 GMT
4. Authorization: AWS AKIAIOSFODNN7EXAMPLE:0RQf4/cRonhpaBX5sCYVf1bNRuU=
```
You can use the other request parameters to retrieve a subset of all versions of the object\. For more information, see [GET Bucket](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGET.html) in the *Amazon Simple Storage Service API Reference*\.

**Example — Retrieving a listing of additional objects if the response is truncated**  
If the number of objects that could be returned in a `GET` request exceeds the value of `max-keys`, the response contains `<isTruncated>true</isTruncated>`, and includes the first key \(in `NextKeyMarker`\) and the first version ID \(in `NextVersionIdMarker`\) that satisfy the request, but were not returned\. You use those returned values as the starting position in a subsequent request to retrieve the additional objects that satisfy the `GET` request\.   
Use the following process to retrieve additional objects that satisfy the original `GET Bucket versions` request from a bucket\. For more information about `key-marker`, `version-id-marker`, `NextKeyMarker`, and `NextVersionIdMarker`, see [GET Bucket](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGET.html)\. in the *Amazon Simple Storage Service API Reference*\.  
The following are additional responses that satisfy the original GET request:  
+ Set the value of `key-marker` to the key returned in `NextKeyMarker` in the previous response\.
+ Set the value of `version-id-marker` to the version ID returned in `NextVersionIdMarker` in the previous response\.
+ Send a `GET Bucket versions` request using `key-marker` and `version-id-marker`\.

**Example — Retrieving objects starting with a specified key and version ID**  

```
1. GET /?versions&key-marker=myObject&version-id-marker=298459348571 HTTP/1.1
2. Host: bucket.s3.amazonaws.com
3. Date: Wed, 28 Oct 2009 22:32:00 GMT
4. Authorization: AWS AKIAIOSFODNN7EXAMPLE:0RQf4/cRonhpaBX5sCYVf1bNRuU=
```