--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Listing object keys programmatically<a name="ListingKeysUsingAPIs"></a>

In Amazon S3, keys can be listed by prefix\. You can choose a common prefix for the names of related keys and mark these keys with a special character that delimits hierarchy\. You can then use the list operation to select and browse keys hierarchically\. This is similar to how files are stored in directories within a file system\. 

Amazon S3 exposes a list operation that lets you enumerate the keys contained in a bucket\. Keys are selected for listing by bucket and prefix\. For example, consider a bucket named "`dictionary`" that contains a key for every English word\. You might make a call to list all the keys in that bucket that start with the letter "q"\. List results are always returned in UTF\-8 binary order\. 

 Both the SOAP and REST list operations return an XML document that contains the names of matching keys and information about the object identified by each key\. 

**Note**  
 SOAP support over HTTP is deprecated, but it is still available over HTTPS\. New Amazon S3 features will not be supported for SOAP\. We recommend that you use either the REST API or the AWS SDKs\. 

Groups of keys that share a prefix terminated by a special delimiter can be rolled up by that common prefix for the purposes of listing\. This enables applications to organize and browse their keys hierarchically, much like how you would organize your files into directories in a file system\. 

For example, to extend the dictionary bucket to contain more than just English words, you might form keys by prefixing each word with its language and a delimiter, such as "`French/logical`"\. Using this naming scheme and the hierarchical listing feature, you could retrieve a list of only French words\. You could also browse the top\-level list of available languages without having to iterate through all the lexicographically intervening keys\. For more information about this aspect of listing, see [Organizing objects using prefixes](using-prefixes.md)\. 

**REST API**  
If your application requires it, you can send REST requests directly\. You can send a GET request to return some or all of the objects in a bucket or you can use selection criteria to return a subset of the objects in a bucket\. For more information, see [GET Bucket \(List Objects\) Version 2](https://docs.aws.amazon.com/AmazonS3/latest/API/v2-RESTBucketGET.html) in the *Amazon Simple Storage Service API Reference*\.

**List implementation efficiency**  
List performance is not substantially affected by the total number of keys in your bucket\. It's also not affected by the presence or absence of the `prefix`, `marker`, `maxkeys`, or `delimiter` arguments\. 

**Iterating through multipage results**  
As buckets can contain a virtually unlimited number of keys, the complete results of a list query can be extremely large\. To manage large result sets, the Amazon S3 API supports pagination to split them into multiple responses\. Each list keys response returns a page of up to 1,000 keys with an indicator indicating if the response is truncated\. You send a series of list keys requests until you have received all the keys\. AWS SDK wrapper libraries provide the same pagination\. 

------
#### [ Java ]

The following example lists the object keys in a bucket\. The example uses pagination to retrieve a set of object keys\. If there are more keys to return after the first page, Amazon S3 includes a continuation token in the response\. The example uses the continuation token in the subsequent request to fetch the next set of object keys\. 

For instructions on creating and testing a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\. 

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.ListObjectsV2Request;
import com.amazonaws.services.s3.model.ListObjectsV2Result;
import com.amazonaws.services.s3.model.S3ObjectSummary;

import java.io.IOException;

public class ListKeys {

    public static void main(String[] args) throws IOException {
        Regions clientRegion = Regions.DEFAULT_REGION;
        String bucketName = "*** Bucket name ***";

        try {
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .withCredentials(new ProfileCredentialsProvider())
                    .withRegion(clientRegion)
                    .build();

            System.out.println("Listing objects");

            // maxKeys is set to 2 to demonstrate the use of
            // ListObjectsV2Result.getNextContinuationToken()
            ListObjectsV2Request req = new ListObjectsV2Request().withBucketName(bucketName).withMaxKeys(2);
            ListObjectsV2Result result;

            do {
                result = s3Client.listObjectsV2(req);

                for (S3ObjectSummary objectSummary : result.getObjectSummaries()) {
                    System.out.printf(" - %s (size: %d)\n", objectSummary.getKey(), objectSummary.getSize());
                }
                // If there are more than maxKeys keys in the bucket, get a continuation token
                // and list the next objects.
                String token = result.getNextContinuationToken();
                System.out.println("Next Continuation Token: " + token);
                req.setContinuationToken(token);
            } while (result.isTruncated());
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

The following C\# example lists the object keys for a bucket\. In the example, pagination is used to retrieve a set of object keys\. If there are more keys to return, Amazon S3 includes a continuation token in the response\. The code uses the continuation token in the subsequent request to fetch the next set of object keys\.

 For instructions on how to create and test a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\.

```
using Amazon;
using Amazon.S3;
using Amazon.S3.Model;
using System;
using System.Threading.Tasks;

namespace Amazon.DocSamples.S3
{
    class ListObjectsTest
    {
        private const string bucketName = "*** bucket name ***";
        // Specify your bucket region (an example region is shown).
        private static readonly RegionEndpoint bucketRegion = RegionEndpoint.USWest2;

        private static IAmazonS3 client;

        public static void Main()
        {
            client = new AmazonS3Client(bucketRegion);
            ListingObjectsAsync().Wait();
        }

        static async Task ListingObjectsAsync()
        {
            try
            {
                ListObjectsV2Request request = new ListObjectsV2Request
                {
                    BucketName = bucketName,
                    MaxKeys = 10
                };
                ListObjectsV2Response response;
                do
                {
                    response = await client.ListObjectsV2Async(request);

                    // Process the response.
                    foreach (S3Object entry in response.S3Objects)
                    {
                        Console.WriteLine("key = {0} size = {1}",
                            entry.Key, entry.Size);
                    }
                    Console.WriteLine("Next Continuation Token: {0}", response.NextContinuationToken);
                    request.ContinuationToken = response.NextContinuationToken;
                } while (response.IsTruncated);
            }
            catch (AmazonS3Exception amazonS3Exception)
            {
                Console.WriteLine("S3 error occurred. Exception: " + amazonS3Exception.ToString());
                Console.ReadKey();
            }
            catch (Exception e)
            {
                Console.WriteLine("Exception: " + e.ToString());
                Console.ReadKey();
            }
        }
    }
}
```

------
#### [ PHP ]

This example guides you through using classes from version 3 of the AWS SDK for PHP to list the object keys contained in an Amazon S3 bucket\. 

 This example assumes that you are already following the instructions for [Using the AWS SDK for PHP and Running PHP Examples](UsingTheMPphpAPI.md) and have the AWS SDK for PHP properly installed\. 

To list the object keys contained in a bucket using the AWS SDK for PHP, you first must list the objects contained in the bucket and then extract the key from each of the listed objects\. When listing objects in a bucket you have the option of using the low\-level [Aws\\S3\\S3Client::listObjects\(\)](https://docs.aws.amazon.com/aws-sdk-php/v3/api/api-s3-2006-03-01.html#listobjects) method or the high\-level [Aws\\ResultPaginator](https://docs.aws.amazon.com/aws-sdk-php/v3/api/class-Aws.ResultPaginator.html) class\. 

The low\-level `listObjects()` method maps to the underlying Amazon S3 REST API\. Each `listObjects()` request returns a page of up to 1,000 objects\. If you have more than 1,000 objects in the bucket, your response will be truncated and you must send another `listObjects()` request to retrieve the next set of 1,000 objects\. 

You can use the high\-level `ListObjects` paginator to make it easier to list the objects contained in a bucket\. To use the `ListObjects` paginator to create an object list, run the Amazon S3 client [getPaginator\(\)](https://docs.aws.amazon.com/aws-sdk-php/v3/api/class-Aws.AwsClientInterface.html#_getPaginator) method \(inherited from the [Aws/AwsClientInterface](https://docs.aws.amazon.com/aws-sdk-php/v3/api/class-Aws.AwsClientInterface.html) class\) with the `ListObjects` command as the first argument and an array to contain the returned objects from the specified bucket as the second argument\. 

When used as a `ListObjects` paginator, the `getPaginator()` method returns all the objects contained in the specified bucket\. There is no 1,000 object limit, so you don't need to worry whether the response is truncated\.

The following tasks guide you through using the PHP Amazon S3 client methods to list the objects contained in a bucket from which you can list the object keys\.

**Example Listing object keys**  
The following PHP example demonstrates how to list the keys from a specified bucket\. It shows how to use the high\-level `getIterator()` method to list the objects in a bucket and then extract the key from each of the objects in the list\. It also shows how to use the low\-level `listObjects()` method to list the objects in a bucket and then extract the key from each of the objects in the list returned\. For information about running the PHP examples in this guide, see [Running PHP Examples](UsingTheMPphpAPI.md#running-php-samples)\.   

```
 require 'vendor/autoload.php';

use Aws\S3\S3Client;
use Aws\S3\Exception\S3Exception;

$bucket = '*** Your Bucket Name ***';

// Instantiate the client.
$s3 = new S3Client([
    'version' => 'latest',
    'region'  => 'us-east-1'
]);

// Use the high-level iterators (returns ALL of your objects).
try {
    $results = $s3->getPaginator('ListObjects', [
        'Bucket' => $bucket
    ]);

    foreach ($results as $result) {
        foreach ($result['Contents'] as $object) {
            echo $object['Key'] . PHP_EOL;
        }
    }
} catch (S3Exception $e) {
    echo $e->getMessage() . PHP_EOL;
}

// Use the plain API (returns ONLY up to 1000 of your objects).
try {
    $objects = $s3->listObjects([
        'Bucket' => $bucket
    ]);
    foreach ($objects['Contents']  as $object) {
        echo $object['Key'] . PHP_EOL;
    }
} catch (S3Exception $e) {
    echo $e->getMessage() . PHP_EOL;
}
```

------