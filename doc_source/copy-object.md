# Copying objects<a name="copy-object"></a>

The copy operation creates a copy of an object that is already stored in Amazon S3\.

You can create a copy of your object up to 5 GB in a single atomic operation\. However, to copy an object that is greater than 5 GB, you must use the multipart upload API\.

Using the `copy` operation, you can:
+ Create additional copies of objects 
+ Rename objects by copying them and deleting the original ones 
+ Move objects across Amazon S3 locations \(for example, us\-west\-1 and Europe\) 
+ Change object metadata

  Each Amazon S3 object has metadata\. It is a set of name\-value pairs\. You can set object metadata at the time you upload it\. After you upload the object, you cannot modify object metadata\. The only way to modify object metadata is to make a copy of the object and set the metadata\. In the copy operation, set the same object as the source and target\. 

Each object has metadata\. Some of it is system metadata and other is user\-defined\. You can control some of the system metadata, such as the storage class configuration to use for the object, and you can configure server\-side encryption\. When you copy an object, user\-controlled system metadata and user\-defined metadata are also copied\. Amazon S3 resets the system\-controlled metadata\. For example, when you copy an object, Amazon S3 resets the creation date of the copied object\. You don't need to set any of these values in your copy request\. 

When copying an object, you might decide to update some of the metadata values\. For example, if your source object is configured to use S3 Standard storage, you might choose to use S3 Intelligent\-Tiering for the object copy\. You might also decide to alter some of the user\-defined metadata values present on the source object\. If you choose to update any of the object's user\-configurable metadata \(system or user\-defined\) during the copy, then you must explicitly specify all of the user\-configurable metadata present on the source object in your request, even if you are changing only one of the metadata values\.

For more information about the object metadata, see [Working with object metadata](UsingMetadata.md)\.

**Note**  
Copying objects across locations incurs bandwidth charges\. 
 If the source object is archived in `S3 Glacier Flexible Retrieval` or `S3 Glacier Deep Archive`, you must first restore a temporary copy before you can copy the object to another bucket\. For information about archiving objects, see [Transitioning to the S3 Glacier Flexible Retrieval and S3 Glacier Deep Archive storage classes \(object archival\)](lifecycle-transition-general-considerations.md#before-deciding-to-archive-objects)\. 

Amazon S3 automatically encrypts all new objects that are copied to an S3 bucket\. If you don't specify encryption information in your copy request, the encryption setting of the target object is set to the default encryption configuration of the destination bucket\. By default, all buckets have a base level of encryption configuration that uses server\-side encryption with Amazon S3 managed keys \(SSE\-S3\)\. If the destination bucket has a default encryption configuration that uses server\-side encryption with an AWS Key Management Service \(AWS KMS\) key \(SSE\-KMS\), or a customer\-provided encryption key \(SSE\-C\), Amazon S3 uses the corresponding KMS key, or a customer\-provided key to encrypt the target object copy\. When copying an object, if you want to use a different type of encryption setting for the target object, you can request that Amazon S3 encrypts the target object with a KMS key, an Amazon S3 managed key, or a customer\-provided key\. If the encryption setting in your request is different from the default encryption configuration of the destination bucket, the encryption setting in your request takes precedence\. If the source object for the copy is stored in Amazon S3 using SSE\-C, you must provide the necessary encryption information in your request so that Amazon S3 can decrypt the object for copying\. For more information, see [Protecting data using encryption](UsingEncryption.md)\. 

When copying objects, you can choose to use a different checksum algorithm for the object\. Whether you choose to use the same algorithm or a new one, Amazon S3 calculates a new checksum value after the object is copied\. Amazon S3 does not directly copy the value of the checksum\. The checksum value of objects that were loaded using multipart uploads might change\. For more information about how the checksum is calculated, see [Using part\-level checksums for multipart uploads](checking-object-integrity.md#large-object-checksums)\.

To copy more than one Amazon S3 object with a single request, you can use Amazon S3 batch operations\. You provide S3 Batch Operations with a list of objects to operate on\. S3 Batch Operations calls the respective API to perform the specified operation\. A single Batch Operations job can perform the specified operation on billions of objects containing exabytes of data\. 

The S3 Batch Operations feature tracks progress, sends notifications, and stores a detailed completion report of all actions, providing a fully managed, auditable, serverless experience\. You can use S3 Batch Operations through the AWS Management Console, AWS CLI, AWS SDKs, or REST API\. For more information, see [S3 Batch Operations basics](batch-ops.md#batch-ops-basics)\.



## To copy an object<a name="CopyingObjectsExamples"></a>

To copy an object, use the following methods\.

### Using the S3 console<a name="copying-moving-console"></a>

In the Amazon S3 console, you can copy or move an object\. For more information, see the following procedures\.

**Note**  
Objects encrypted with customer\-provided encryption keys \(SSE\-C\) cannot be copied or moved using the S3 console\. To copy or move objects encrypted with SSE\-C, use the AWS CLI, AWS SDK, or the Amazon S3 REST API\.
When copying an object by using the Amazon S3 console, you must grant permission `s3:ListAllMyBuckets`\. The console needs this permission to validate the copy operation\.

**To copy an object**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Navigate to the Amazon S3 bucket or folder that contains the objects that you want to copy\.

1. Select the check box to the left of the names of the objects that you want to copy\.

1. Choose **Actions** and choose **Copy** from the list of options that appears\.

   Alternatively, choose **Copy** from the options in the upper\-right corner\. 

1. Select the destination type and destination account\. To specify the destination path, choose **Browse S3**, navigate to the destination, and select the check box to the left of the destination\. Choose **Choose destination** in the lower\-right corner\. 

   Alternatively, enter the destination path\. 

1. If you do *not* have bucket versioning enabled, you might be asked to acknowledge that existing objects with the same name are overwritten\. If this is OK, select the check box and proceed\. If you want to keep all versions of objects in this bucket, select **Enable Bucket Versioning**\. You can also update the default encryption and S3 Object Lock properties\.

1. Under **Additional checksums**, choose whether you want to copy the objects using the existing checksum function or replace the existing checksum function with a new one\. When you uploaded the objects, you had the option to specify the checksum algorithm that was used to verify data integrity\. When copying the object, you have the option to choose a new function\. If you did not originally specify an additional checksum, you can use this section of the copy options to add one\.
**Note**  
Even if you opt to use the same checksum function, your checksum value might change if you copy the object and it is over 16 MB in size\. The checksum value might change because of how checksums are calculated for multipart uploads\. For more information about how the checksum might change when copying the object, see [Using part\-level checksums for multipart uploads](checking-object-integrity.md#large-object-checksums)\.

   To change the checksum function, choose **Replace with a new checksum function**\. Choose the new checksum function from the box\. When the object is copied over, the new checksum is calculated and stored using the specified algorithm\.

1. Choose **Copy** in the bottom\-right corner\. Amazon S3 copies your objects to the destination\.

**To move objects**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Navigate to the Amazon S3 bucket or folder that contains the objects that you want to move\.

1. Select the check box to the left of the names of the objects that you want to move\.

1. Choose **Actions** and choose **Move** from the list of options that appears\.

   Alternatively, choose **Move** from the options in the upper\-right corner\. 

1. To specify the destination path, choose **Browse S3**, navigate to the destination, and select the check box to the left of the destination\. Choose **Choose destination** in the lower\-right corner\. 

   Alternatively, enter the destination path\. 

1. If you do *not* have bucket versioning enabled, you might be asked to acknowledge that existing objects with the same name are overwritten\. If this is OK, select the check box and proceed\. If you want to keep all versions of objects in this bucket, select **Enable Bucket Versioning**\. You can also update the default encryption and Object Lock properties\.

1. Choose **Move** in the bottom\-right corner\. Amazon S3 moves your objects to the destination\.

**Note**  
This action creates a copy of all specified objects with updated settings, updates the last\-modified date in the specified location, and adds a delete marker to the original object\. 
When moving folders, wait for the move action to finish before making additional changes in the folders\. 
This action updates metadata for bucket versioning, encryption, Object Lock features, and archived objects\. 

### Using the AWS SDKs<a name="CopyingObjectsUsingSDKs"></a>

The examples in this section show how to copy objects up to 5 GB in a single operation\. For copying objects greater than 5 GB, you must use multipart upload API\. For more information, see [Copying an object using multipart upload](CopyingObjectsMPUapi.md)\.

------
#### [ Java ]

**Example**  
The following example copies an object in Amazon S3 using the AWS SDK for Java\. For instructions on creating and testing a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\.   

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.CopyObjectRequest;

import java.io.IOException;

public class CopyObjectSingleOperation {

    public static void main(String[] args) throws IOException {
        Regions clientRegion = Regions.DEFAULT_REGION;
        String bucketName = "*** Bucket name ***";
        String sourceKey = "*** Source object key *** ";
        String destinationKey = "*** Destination object key ***";

        try {
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .withCredentials(new ProfileCredentialsProvider())
                    .withRegion(clientRegion)
                    .build();

            // Copy the object into a new object in the same bucket.
            CopyObjectRequest copyObjRequest = new CopyObjectRequest(bucketName, sourceKey, bucketName, destinationKey);
            s3Client.copyObject(copyObjRequest);
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

The following C\# example uses the high\-level AWS SDK for \.NET to copy objects that are as large as 5 GB in a single operation\. For objects that are larger than 5 GB, use the multipart upload copy example described in [Copying an object using multipart upload](CopyingObjectsMPUapi.md)\.

This example makes a copy of an object that is a maximum of 5 GB\. For information about the example's compatibility with a specific version of the AWS SDK for \.NET and instructions on how to create and test a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\.

```
using Amazon;
using Amazon.S3;
using Amazon.S3.Model;
using System;
using System.Threading.Tasks;

namespace Amazon.DocSamples.S3
{
    class CopyObjectTest
    {
        private const string sourceBucket = "*** provide the name of the bucket with source object ***";
        private const string destinationBucket = "*** provide the name of the bucket to copy the object to ***";
        private const string objectKey = "*** provide the name of object to copy ***";
        private const string destObjectKey = "*** provide the destination object key name ***";
        // Specify your bucket region (an example region is shown).
        private static readonly RegionEndpoint bucketRegion = RegionEndpoint.USWest2; 
        private static IAmazonS3 s3Client;

        public static void Main()
        {
            s3Client = new AmazonS3Client(bucketRegion);
            Console.WriteLine("Copying an object");
            CopyingObjectAsync().Wait();
        }

        private static async Task CopyingObjectAsync()
        {
            try
            {
                CopyObjectRequest request = new CopyObjectRequest
                {
                    SourceBucket = sourceBucket,
                    SourceKey = objectKey,
                    DestinationBucket = destinationBucket,
                    DestinationKey = destObjectKey
                };
                CopyObjectResponse response = await s3Client.CopyObjectAsync(request);
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

 This topic guides you through using classes from version 3 of the AWS SDK for PHP to copy a single object and multiple objects within Amazon S3, from one bucket to another or within the same bucket\.

 This topic assumes that you are already following the instructions for [Using the AWS SDK for PHP and Running PHP Examples](UsingTheMPphpAPI.md) and have the AWS SDK for PHP properly installed\.

The following PHP example illustrates the use of the `copyObject()` method to copy a single object within Amazon S3 and using a batch of calls to `CopyObject` using the `getcommand()` method to make multiple copies of an object\.


**Copying objects**  

|  |  | 
| --- |--- |
|  1  |  Create an instance of an Amazon S3 client by using the `Aws\S3\S3Client` class constructor\.  | 
|  2  |  To make multiple copies of an object, you run a batch of calls to the Amazon S3 client [getCommand\(\)](https://docs.aws.amazon.com/aws-sdk-php/v3/api/class-Aws.AwsClientInterface.html#_getCommand) method, which is inherited from the [Aws\\CommandInterface](https://docs.aws.amazon.com/aws-sdk-php/v3/api/class-Aws.CommandInterface.html) class\. You provide the `CopyObject` command as the first argument and an array containing the source bucket, source key name, target bucket, and target key name as the second argument\.   | 

```
 require 'vendor/autoload.php';

use Aws\S3\S3Client;

$sourceBucket = '*** Your Source Bucket Name ***';
$sourceKeyname = '*** Your Source Object Key ***';
$targetBucket = '*** Your Target Bucket Name ***';

$s3 = new S3Client([
    'version' => 'latest',
    'region'  => 'us-east-1'
]);

// Copy an object.
$s3->copyObject([
    'Bucket'     => $targetBucket,
    'Key'        => "{$sourceKeyname}-copy",
    'CopySource' => "{$sourceBucket}/{$sourceKeyname}",
]);

// Perform a batch of CopyObject operations.
$batch = array();
for ($i = 1; $i <= 3; $i++) {
    $batch[] = $s3->getCommand('CopyObject', [
        'Bucket'     => $targetBucket,
        'Key'        => "{targetKeyname}-{$i}",
        'CopySource' => "{$sourceBucket}/{$sourceKeyname}",
    ]);
}
try {
    $results = CommandPool::batch($s3, $batch);
    foreach($results as $result) {
        if ($result instanceof ResultInterface) {
            // Result handling here
        }
        if ($result instanceof AwsException) {
            // AwsException handling here
        }
    }
} catch (\Exception $e) {
    // General error handling here
}
```

------
#### [ Ruby ]

The following tasks guide you through using the Ruby classes to copy an object in Amazon S3 from one bucket to another or within the same bucket\. 


**Copying objects**  

|  |  | 
| --- |--- |
|  1  |  Use the Amazon S3 modularized gem for version 3 of the AWS SDK for Ruby, require 'aws\-sdk\-s3', and provide your AWS credentials\. For more information about how to provide your credentials, see [Making requests using AWS account or IAM user credentials](AuthUsingAcctOrUserCredentials.md)\.  | 
|  2  |  Provide the request information, such as source bucket name, source key name, destination bucket name, and destination key\.   | 

 The following Ruby code example demonstrates the preceding tasks using the `#copy_object` method to copy an object from one bucket to another\.

```
require "aws-sdk-s3"

# Wraps Amazon S3 object actions.
class ObjectCopyWrapper
  attr_reader :source_object

  # @param source_object [Aws::S3::Object] An existing Amazon S3 object. This is used as the source object for
  #                                        copy actions.
  def initialize(source_object)
    @source_object = source_object
  end

  # Copy the source object to the specified target bucket and rename it with the target key.
  #
  # @param target_bucket [Aws::S3::Bucket] An existing Amazon S3 bucket where the object is copied.
  # @param target_object_key [String] The key to give the copy of the object.
  # @return [Aws::S3::Object, nil] The copied object when successful; otherwise, nil.
  def copy_object(target_bucket, target_object_key)
    @source_object.copy_to(bucket: target_bucket.name, key: target_object_key)
    target_bucket.object(target_object_key)
  rescue Aws::Errors::ServiceError => e
    puts "Couldn't copy #{@source_object.key} to #{target_object_key}. Here's why: #{e.message}"
  end
end

# Replace the source and target bucket names with existing buckets you own and replace the source object key
# with an existing object in the source bucket.
def run_demo
  source_bucket_name = "doc-example-bucket1"
  source_key = "my-source-file.txt"
  target_bucket_name = "doc-example-bucket2"
  target_key = "my-target-file.txt"

  source_bucket = Aws::S3::Bucket.new(source_bucket_name)
  wrapper = ObjectCopyWrapper.new(source_bucket.object(source_key))
  target_bucket = Aws::S3::Bucket.new(target_bucket_name)
  target_object = wrapper.copy_object(target_bucket, target_key)
  return unless target_object

  puts "Copied #{source_key} from #{source_bucket_name} to #{target_object.bucket_name}:#{target_object.key}."
end

run_demo if $PROGRAM_NAME == __FILE__
```

------

### Copying an object using the REST API<a name="CopyingObjectUsingREST"></a>

This example describes how to copy an object using REST\. For more information about the REST API, go to [PUT Object \(Copy\)](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectCOPY.html)\.

This example copies the `flotsam` object from the `pacific` bucket to the `jetsam` object of the `atlantic` bucket, preserving its metadata\.

```
1. PUT /jetsam HTTP/1.1
2. Host: atlantic.s3.amazonaws.com
3. x-amz-copy-source: /pacific/flotsam
4. Authorization: AWS AKIAIOSFODNN7EXAMPLE:ENoSbxYByFA0UGLZUqJN5EUnLDg=
5. Date: Wed, 20 Feb 2008 22:12:21 +0000
```

The signature was generated from the following information\.

```
1. PUT\r\n
2. \r\n
3. \r\n
4. Wed, 20 Feb 2008 22:12:21 +0000\r\n
5. 
6. x-amz-copy-source:/pacific/flotsam\r\n
7. /atlantic/jetsam
```

Amazon S3 returns the following response that specifies the ETag of the object and when it was last modified\.

```
 1. HTTP/1.1 200 OK
 2. x-amz-id-2: Vyaxt7qEbzv34BnSu5hctyyNSlHTYZFMWK4FtzO+iX8JQNyaLdTshL0KxatbaOZt
 3. x-amz-request-id: 6B13C3C5B34AF333
 4. Date: Wed, 20 Feb 2008 22:13:01 +0000
 5. 
 6. Content-Type: application/xml
 7. Transfer-Encoding: chunked
 8. Connection: close
 9. Server: AmazonS3
10. <?xml version="1.0" encoding="UTF-8"?>
11. 
12. <CopyObjectResult>
13.    <LastModified>2008-02-20T22:13:01</LastModified>
14.    <ETag>"7e9c608af58950deeb370c98608ed097"</ETag>
15. </CopyObjectResult>
```