# Making requests using AWS account or IAM user credentials<a name="AuthUsingAcctOrUserCredentials"></a>

You can use your AWS account or IAM user security credentials to send authenticated requests to Amazon S3\. This section provides examples of how you can send authenticated requests using the AWS SDK for Java, AWS SDK for \.NET, and AWS SDK for PHP\. For a list of available AWS SDKs, go to [Sample Code and Libraries](https://aws.amazon.com/code/)\. 

Each of these AWS SDKs uses an SDK\-specific credentials provider chain to find and use credentials and perform actions on behalf of the credentials owner\. What all these credentials provider chains have in common is that they all look for your local AWS credentials file\. 

For more information, see the topics below:

**Topics**
+ [To create a local AWS credentials file](#create-local-aws-credentials-file)
+ [Sending authenticated requests using the AWS SDKs](#send-authenticated-request-SDKs)
+ [Related resources](#RelatedResources002)

## To create a local AWS credentials file<a name="create-local-aws-credentials-file"></a>

The easiest way to configure credentials for your AWS SDKs is to use an AWS credentials file\. If you use the AWS Command Line Interface \(AWS CLI\), you may already have a local AWS credentials file configured\. Otherwise, use the following procedure to set up a credentials file:

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. Create a new user with permissions limited to the services and actions that you want your code to have access to\. For more information about creating a new IAM user, see [Creating IAM Users \(Console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html#id_users_create_console), and follow the instructions through step 8\.

1. Choose **Download \.csv** to save a local copy of your AWS credentials\.

1. On your computer, navigate to your home directory, and create an `.aws` directory\. On Unix\-based systems, such as Linux or OS X, this is in the following location:

   ```
   ~/.aws
   ```

   On Windows, this is in the following location:

   ```
   %HOMEPATH%\.aws
   ```

1. In the `.aws` directory, create a new file named `credentials`\.

1. Open the credentials \.csv file that you downloaded from the IAM console, and copy its contents into the `credentials` file using the following format:

   ```
   [default]
   aws_access_key_id = your_access_key_id
   aws_secret_access_key = your_secret_access_key
   ```

1. Save the `credentials` file, and delete the \.csv file that you downloaded in step 3\.

Your shared credentials file is now configured on your local computer, and it's ready to be used with the AWS SDKs\.

## Sending authenticated requests using the AWS SDKs<a name="send-authenticated-request-SDKs"></a>

Use the AWS SDKs to send authenticated requests\.

------
#### [ Java ]

To send authenticated requests to Amazon S3 using your AWS account or IAM user credentials, do the following: 
+ Use the `AmazonS3ClientBuilder` class to create an `AmazonS3Client` instance\.
+ Run one of the `AmazonS3Client` methods to send requests to Amazon S3\. The client generates the necessary signature from the credentials that you provide and includes it in the request\.

 The following example performs the preceding tasks\. For information on creating and testing a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\. 

**Example**  

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.ListObjectsRequest;
import com.amazonaws.services.s3.model.ObjectListing;
import com.amazonaws.services.s3.model.S3ObjectSummary;

import java.io.IOException;
import java.util.List;

public class MakingRequests {

    public static void main(String[] args) throws IOException {
        Regions clientRegion = Regions.DEFAULT_REGION;
        String bucketName = "*** Bucket name ***";

        try {
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .withCredentials(new ProfileCredentialsProvider())
                    .withRegion(clientRegion)
                    .build();

            // Get a list of objects in the bucket, two at a time, and 
            // print the name and size of each object.
            ListObjectsRequest listRequest = new ListObjectsRequest().withBucketName(bucketName).withMaxKeys(2);
            ObjectListing objects = s3Client.listObjects(listRequest);
            while (true) {
                List<S3ObjectSummary> summaries = objects.getObjectSummaries();
                for (S3ObjectSummary summary : summaries) {
                    System.out.printf("Object \"%s\" retrieved with size %d\n", summary.getKey(), summary.getSize());
                }
                if (objects.isTruncated()) {
                    objects = s3Client.listNextBatchOfObjects(objects);
                } else {
                    break;
                }
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

To send authenticated requests using your AWS account or IAM user credentials:
+ Create an instance of the `AmazonS3Client` class\. 
+ Run one of the `AmazonS3Client` methods to send requests to Amazon S3\. The client generates the necessary signature from the credentials that you provide and includes it in the request it sends to Amazon S3\. 

For more information, see [Making requests using AWS account or IAM user credentials](#AuthUsingAcctOrUserCredentials)\. 

**Note**  
You can create the `AmazonS3Client` client without providing your security credentials\. Requests sent using this client are anonymous requests, without a signature\. Amazon S3 returns an error if you send anonymous requests for a resource that is not publicly available\.
You can create an AWS account and create the required user accounts\. You can also manage credentials for those user accounts\. You need these credentials to perform the task in the following example\. For more informatory, see [Configure AWS credentials](https://docs.aws.amazon.com/sdk-for-net/latest/developer-guide/net-dg-config-creds.html) in the *AWS SDK for \.NET Developer Guide*\.  
You can then also configure your application to actively retrieve profiles and credentials, and then explicitly use those credentials when creating an AWS service client\. For more information, see [Accessing credentials and profiles in an application](https://docs.aws.amazon.com/sdk-for-net/latest/developer-guide/creds-locate.html) in the *AWS SDK for \.NET Developer Guide*\.

The following C\# example shows how to perform the preceding tasks\. For information about running the \.NET examples in this guide and for instructions on how to store your credentials in a configuration file, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\.

**Example**  

```
using Amazon;
using Amazon.S3;
using Amazon.S3.Model;
using System;
using System.Threading.Tasks;

namespace Amazon.DocSamples.S3
{
    class MakeS3RequestTest
    {
        private const string bucketName = "*** bucket name ***"; 
        // Specify your bucket region (an example region is shown).
        private static readonly RegionEndpoint bucketRegion = RegionEndpoint.USWest2;
        private static IAmazonS3 client;

        public static void Main()
        {
            using (client = new AmazonS3Client(bucketRegion))
            {
                Console.WriteLine("Listing objects stored in a bucket");
                ListingObjectsAsync().Wait();
            }
        }

        static async Task ListingObjectsAsync()
        {
            try
            {
                ListObjectsRequest request = new ListObjectsRequest
                {
                    BucketName = bucketName,
                    MaxKeys = 2
                };
                do
                {
                    ListObjectsResponse response = await client.ListObjectsAsync(request);
                    // Process the response.
                    foreach (S3Object entry in response.S3Objects)
                    {
                        Console.WriteLine("key = {0} size = {1}",
                            entry.Key, entry.Size);
                    }

                    // If the response is truncated, set the marker to get the next 
                    // set of keys.
                    if (response.IsTruncated)
                    {
                        request.Marker = response.NextMarker;
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

For working examples, see [Amazon S3 objects overview](UsingObjects.md) and [Buckets overview](UsingBucket.md)\. You can test these examples using your AWS account or an IAM user credentials\. 

For example, to list all the object keys in your bucket, see [Listing object keys programmatically](ListingKeysUsingAPIs.md)\. 

------
#### [ PHP ]

This section explains how to use a class from version 3 of the AWS SDK for PHP to send authenticated requests using your AWS account or IAM user credentials\. It assumes that you are already following the instructions for [Using the AWS SDK for PHP and Running PHP Examples](UsingTheMPphpAPI.md) and have the AWS SDK for PHP properly installed\. 

The following PHP example shows how the client makes a request using your security credentials to list all of the buckets for your account\. 

**Example**  

```
 
require 'vendor/autoload.php';

use Aws\Sts\StsClient;
use Aws\S3\S3Client;
use Aws\S3\Exception\S3Exception;

$bucket = '*** Your Bucket Name ***';

$s3 = new S3Client([
    'region' => 'us-east-1',
    'version' => 'latest',
]);

// Retrieve the list of buckets.
$result = $s3->listBuckets();

try {
    // Retrieve a paginator for listing objects.
    $objects = $s3->getPaginator('ListObjects', [
        'Bucket' => $bucket
    ]);

    echo "Keys retrieved!" . PHP_EOL;
    
    // Print the list of objects to the page.
    foreach ($objects as $object) {
        echo $object['Key'] . PHP_EOL;
    }
} catch (S3Exception $e) {
    echo $e->getMessage() . PHP_EOL;
}
```

**Note**  
You can create the `S3Client` client without providing your security credentials\. Requests sent using this client are anonymous requests, without a signature\. Amazon S3 returns an error if you send anonymous requests for a resource that is not publicly available\. For more information, see [Creating Anonymous Clients](https://docs.aws.amazon.com/sdk-for-php/v3/developer-guide/guide_credentials_anonymous.html) in the [AWS SDK for PHP Documentation](http://aws.amazon.com/documentation/sdk-for-php/)\.

For working examples, see [Amazon S3 objects overview](UsingObjects.md)\. You can test these examples using your AWS account or IAM user credentials\. 

For an example of listing object keys in a bucket, see [Listing object keys programmatically](ListingKeysUsingAPIs.md)\. 

------
#### [ Ruby ]

Before you can use version 3 of the AWS SDK for Ruby to make calls to Amazon S3, you must set the AWS access credentials that the SDK uses to verify your access to your buckets and objects\. If you have shared credentials set up in the AWS credentials profile on your local system, version 3 of the SDK for Ruby can use those credentials without your having to declare them in your code\. For more information about setting up shared credentials, see [Making requests using AWS account or IAM user credentials](#AuthUsingAcctOrUserCredentials)\.

The following Ruby code snippet uses the credentials in a shared AWS credentials file on a local computer to authenticate a request to get all of the object key names in a specific bucket\. It does the following:

1. Creates an instance of the `Aws::S3::Client` class\. 

1. Makes a request to Amazon S3 by enumerating objects in a bucket using the `list_objects_v2` method of `Aws::S3::Client`\. The client generates the necessary signature value from the credentials in the AWS credentials file on your computer, and includes it in the request it sends to Amazon S3\.

1. Prints the array of object key names to the terminal\.

**Example**  

```
require 'aws-sdk-s3'

# Prints the list of objects in an Amazon S3 bucket.
#
# @param s3_client [Aws::S3::Client] An initialized Amazon S3 client.
# @param bucket_name [String] The bucket's name.
# @return [Boolean] true if all operations succeed; otherwise, false.
# @example
#   s3_client = Aws::S3::Client.new(region: 'us-east-1')
#   exit 1 unless list_bucket_objects?(s3_client, 'doc-example-bucket')
def list_bucket_objects?(s3_client, bucket_name)
  puts "Accessing the bucket named '#{bucket_name}'..."
  objects = s3_client.list_objects_v2(
    bucket: bucket_name,
    max_keys: 50
  )

  if objects.count.positive?
    puts 'The object keys in this bucket are (first 50 objects):'
    objects.contents.each do |object|
      puts object.key
    end
  else
    puts 'No objects found in this bucket.'
  end

  return true
rescue StandardError => e
  puts "Error while accessing the bucket named '#{bucket_name}': #{e.message}"
  return false
end
```

If you don't have a local AWS credentials file, you can still create the `Aws::S3::Client` resource and run code against Amazon S3 buckets and objects\. Requests that are sent using version 3 of the SDK for Ruby are anonymous, with no signature by default\. Amazon S3 returns an error if you send anonymous requests for a resource that's not publicly available\.

You can use and expand the previous code snippet for SDK for Ruby applications, as in the following more robust example\. 

```
require 'aws-sdk-s3'

# Prints a list of objects in an Amazon S3 bucket.
#
# Prerequisites:
#
# - An Amazon S3 bucket.
#
# @param s3_client [Aws::S3::Client] An initialized Amazon S3 client.
# @param bucket_name [String] The bucket's name.
# @return [Boolean] true if all operations succeed; otherwise, false.
# @example
#   s3_client = Aws::S3::Client.new(region: 'us-east-1')
#   exit 1 unless can_list_bucket_objects?(s3_client, 'doc-example-bucket')
def list_bucket_objects?(s3_client, bucket_name)
  puts "Accessing the bucket named '#{bucket_name}'..."
  objects = s3_client.list_objects_v2(
    bucket: bucket_name,
    max_keys: 50
  )

  if objects.count.positive?
    puts 'The object keys in this bucket are (first 50 objects):'
    objects.contents.each do |object|
      puts object.key
    end
  else
    puts 'No objects found in this bucket.'
  end

  return true
rescue StandardError => e
  puts "Error while accessing the bucket named '#{bucket_name}': #{e.message}"
end
```

------

## Related resources<a name="RelatedResources002"></a>
+ [Developing with Amazon S3 using the AWS SDKs, and explorers](UsingAWSSDK.md)
+ [ AWS SDK for PHP for Amazon S3 Aws\\S3\\S3Client Class](https://docs.aws.amazon.com/aws-sdk-php/v3/api/class-Aws.S3.S3Client.html) 
+ [AWS SDK for PHP Documentation](http://aws.amazon.com/documentation/sdk-for-php/)