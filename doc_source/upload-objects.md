# Uploading objects<a name="upload-objects"></a>

When you upload a file to Amazon S3, it is stored as an S3 object\. Objects consist of the file data and metadata that describes the object\. You can have an unlimited number of objects in a bucket\. Before you can upload files to an Amazon S3 bucket, you need write permissions for the bucket\. For more information about access permissions, see [Identity and access management in Amazon S3](s3-access-control.md)\. 

You can upload any file type—images, backups, data, movies, etc\.—into an S3 bucket\. The maximum size of a file that you can upload by using the Amazon S3 console is 160 GB\. To upload a file larger than 160 GB, use the AWS CLI, AWS SDK, or Amazon S3 REST API\.

If you upload an object with a key name that already exists in a versioning\-enabled bucket, Amazon S3 creates another version of the object instead of replacing the existing object\. For more information about versioning, see [Using the S3 console](manage-versioning-examples.md#enable-versioning)\.

 Depending on the size of the data you are uploading, Amazon S3 offers the following options: 
+ **Upload an object in a single operation using the AWS SDKs, REST API, or AWS CLI—**With a single PUT operation, you can upload a single object up to 5 GB in size\.
+ **Upload a single object using the Amazon S3 Console—**With the Amazon S3 Console, you can upload a single object up to 160 GB in size\. 
+ **Upload an object in parts using the AWS SDKs, REST API, or AWS CLI—**Using the multipart upload API, you can upload a single large object, up to 5 TB in size\.

  The multipart upload API is designed to improve the upload experience for larger objects\. You can upload an object in parts\. These object parts can be uploaded independently, in any order, and in parallel\. You can use a multipart upload for objects from 5 MB to 5 TB in size\. For more information, see [Uploading and copying objects using multipart upload](mpuoverview.md)\.

When uploading an object, you can optionally request that Amazon S3 encrypt it before saving it to disk, and decrypt it when you download it\. For more information, see [Protecting data using encryption](UsingEncryption.md)\. 

## Using the S3 console<a name="upload-objects-by-drag-and-drop"></a>

This procedure explains how to upload objects and folders to an S3 bucket using the console\. 

When you upload an object, the object key name is the file name and any optional prefixes\. In the Amazon S3 console, you can create folders to organize your objects\. In Amazon S3, folders are represented as prefixes that appear in the object key name\. If you upload an individual object to a folder in the Amazon S3 console, the folder name is included in the object key name\. 

For example, if you upload an object named `sample1.jpg` to a folder named `backup`, the key name is `backup/sample1.jpg`\. However, the object is displayed in the console as `sample1.jpg` in the `backup` folder\. For more information about key names, see [Working with object metadata](UsingMetadata.md)\.

**Note**  
If you rename an object or change any of the properties in the Amazon S3 console, for example **Storage Class**, ** Encryption**, **Metadata**, a new object is created to replace the old one\. If S3 Versioning is enabled, a new version of the object is created, and the existing object becomes an older version\. The role that changes the property also becomes the owner of the new object or \(object version\)\.

When you upload a folder, Amazon S3 uploads all of the files and subfolders from the specified folder to your bucket\. It then assigns an object key name that is a combination of the uploaded file name and the folder name\. For example, if you upload a folder named `/images` that contains two files, `sample1.jpg` and `sample2.jpg`, Amazon S3 uploads the files and then assigns the corresponding key names, `images/sample1.jpg` and `images/sample2.jpg`\. The key names include the folder name as a prefix\. The Amazon S3 console displays only the part of the key name that follows the last `/`\. For example, within an images folder the `images/sample1.jpg` and `images/sample2.jpg` objects are displayed as `sample1.jpg` and a `sample2.jpg`\.<a name="upload-files-folders"></a>

**To upload folders and files to an S3 bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want to upload your folders or files to\.

1. Choose **Upload**\.

1. In the **Upload** window, do one of the following: 
   + Drag and drop files and folders to the **Upload** window\.
   + Choose **Add file** or **Add folder**, choose files or folders to upload, and choose **Open**\.

1. To enable versioning, under **Destination**, choose **Enable Bucket Versioning**\.

1. To upload the listed files and folders without configuring additional upload options, at the bottom of the page, choose **Upload**\.

   Amazon S3 uploads your objects and folders\. When the upload completes, you can see a success message on the **Upload: status** page\.<a name="configure-additional-properties"></a>

**To configure additional object properties**

1. To change access control list permissions, choose **Permissions**\.

1. Under **Access control list \(ACL\)**, edit the permissions\.

   For information about object access permissions, see [Using the S3 console to set ACL permissions for an object](managing-acls.md#set-object-permissions)\. You can grant read access to your objects to the public \(everyone in the world\) for all of the files that you're uploading\. However, we recommend not changing the default setting for public read access\. Granting public read access is applicable to a small subset of use cases, such as when buckets are used for websites\. You can always change the object permissions after you upload the object\. 

1. To configure other additional properties, choose **Properties**\.

1. Under **Storage class**, choose the storage class for the files you're uploading\.

   For more information about storage classes, see [Using Amazon S3 storage classes](storage-class-intro.md)\.

1. To update the encryption settings for your objects, under **Server\-side encryption settings**, do the following\.

   1. Choose **Specify an encryption key**\.

   1. To encrypt the uploaded files using keys that are managed by Amazon S3, choose **Amazon S3 key \(SSE\-S3\)**\.

      For more information, see [Protecting data using server\-side encryption with Amazon S3\-managed encryption keys \(SSE\-S3\)](UsingServerSideEncryption.md)\.

   1. To encrypt the uploaded files using the AWS Key Management Service \(AWS KMS\), choose **AWS Key Management Service key \(SSE\-KMS\)**\. Then choose an option for **AWS KMS key**\.
      + **AWS managed key** – Choose an [AWS managed key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#aws-managed-cmk)\.
      + **Choose from your KMS root keys** – Choose a [customer managed key ](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html#customer-cmk) from a list of KMS keys in the same Region as your bucket\.

        For more information about creating a customer managed key, see [Creating Keys](https://docs.aws.amazon.com/kms/latest/developerguide/UsingServerSideEncryption.html) in the *AWS Key Management Service Developer Guide*\. For more information about protecting data with AWS KMS, see [Protecting data using server\-side encryption with AWS Key Management Service \(SSE\-KMS\)](UsingKMSEncryption.md)\.
      + **Enter KMS root key ARN** – Specify the AWS KMS key ARN for a customer managed key, and enter the Amazon Resource Name \(ARN\)\.

        You can use the KMS root key ARN to give an external account the ability to use an object that is protected by an AWS KMS key\. To do this, choose **Enter KMS root key ARN**, and enter the Amazon Resource Name \(ARN\) for the external account\. Administrators of an external account that have usage permissions to an object protected by your KMS key can further restrict access by creating a resource\-level IAM policy\. 
**Note**  
To encrypt objects in a bucket, you can use only AWS KMS keys that are available in the same AWS Region as the bucket\. 

1. To use additional checksums, choose **On**\. Then for **Checksum function**, choose the function that you would like to use\. Amazon S3 calculates and stores the checksum value after it receives the entire object\. You can use the **Precalculated value** box to supply a precalculated value\. If you do, Amazon S3 compares the value that you provided to the value that it calculates\. If the two values do not match, Amazon S3 generates an error\.

   Additional checksums enable you to specify the checksum algorithm that you would like to use to verify your data\. For more information about additional checksums, see [Checking object integrity](checking-object-integrity.md)\.

1. To add tags to all of the objects that you are uploading, choose **Add tag**\. Type a tag name in the **Key** field\. Type a value for the tag\.

   Object tagging gives you a way to categorize storage\. Each tag is a key\-value pair\. Key and tag values are case sensitive\. You can have up to 10 tags per object\. A tag key can be up to 128 Unicode characters in length and tag values can be up to 255 Unicode characters in length\. For more information about object tags, see [Categorizing your storage using tags](object-tagging.md)\.

1. To add metadata, choose **Add metadata**\.

   1. Under **Type**, choose **System defined** or **User defined**\.

      For system\-defined metadata, you can select common HTTP headers, such as **Content\-Type** and **Content\-Disposition**\. For a list of system\-defined metadata and information about whether you can add the value, see [System\-defined object metadata](UsingMetadata.md#SysMetadata)\. Any metadata starting with prefix `x-amz-meta-` is treated as user\-defined metadata\. User\-defined metadata is stored with the object and is returned when you download the object\. Both the keys and their values must conform to US\-ASCII standards\. User\-defined metadata can be as large as 2 KB\. For more information about system\-defined and user\-defined metadata, see [Working with object metadata](UsingMetadata.md)\.

   1. For **Key**, choose a key\.

   1. Type a value for the key\. 

1. To upload your objects, choose **Upload**\.

   Amazon S3 uploads your object\. When the upload completes, you can see a success message on the **Upload: status** page\.

1. Choose **Exit**\.

## Using the AWS SDKs<a name="UploadInSingleOp"></a>

You can use the AWS SDK to upload objects in Amazon S3\. The SDK provides wrapper libraries for you to upload data easily\. For information, see the [List of supported SDKs](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html#API_PutObject_SeeAlso)\.

Here are a few examples with a few select SDKs:

------
#### [ \.NET ]

The following C\# code example creates two objects with two `PutObjectRequest` requests:
+ The first `PutObjectRequest` request saves a text string as sample object data\. It also specifies the bucket and object key names\. 
+ The second `PutObjectRequest` request uploads a file by specifying the file name\. This request also specifies the `ContentType` header and optional object metadata \(a title\)\. 

For instructions on how to create and test a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\.

```
using Amazon;
using Amazon.S3;
using Amazon.S3.Model;
using System;
using System.Threading.Tasks;

namespace Amazon.DocSamples.S3
{
    class UploadObjectTest
    {
        private const string bucketName = "*** bucket name ***";
        // For simplicity the example creates two objects from the same file.
        // You specify key names for these objects.
        private const string keyName1 = "*** key name for first object created ***";
        private const string keyName2 = "*** key name for second object created ***";
        private const string filePath = @"*** file path ***";
        private static readonly RegionEndpoint bucketRegion = RegionEndpoint.EUWest1; 

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
                // 1. Put object-specify only key name for the new object.
                var putRequest1 = new PutObjectRequest
                {
                    BucketName = bucketName,
                    Key = keyName1,
                    ContentBody = "sample text"
                };

                PutObjectResponse response1 = await client.PutObjectAsync(putRequest1);

                // 2. Put the object-set ContentType and add metadata.
                var putRequest2 = new PutObjectRequest
                {
                    BucketName = bucketName,
                    Key = keyName2,
                    FilePath = filePath,
                    ContentType = "text/plain"
                };
                
                putRequest2.Metadata.Add("x-amz-meta-title", "someTitle");
                PutObjectResponse response2 = await client.PutObjectAsync(putRequest2);
            }
            catch (AmazonS3Exception e)
            {
                Console.WriteLine(
                        "Error encountered ***. Message:'{0}' when writing an object"
                        , e.Message);
            }
            catch (Exception e)
            {
                Console.WriteLine(
                    "Unknown encountered on server. Message:'{0}' when writing an object"
                    , e.Message);
            }
        }
    }
}
```

------
#### [ Java ]

The following example creates two objects\. The first object has a text string as data, and the second object is a file\. The example creates the first object by specifying the bucket name, object key, and text data directly in a call to `AmazonS3Client.putObject()`\. The example creates the second object by using a `PutObjectRequest` that specifies the bucket name, object key, and file path\. The `PutObjectRequest` also specifies the `ContentType` header and title metadata\.

 For instructions on creating and testing a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\. 

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.ObjectMetadata;
import com.amazonaws.services.s3.model.PutObjectRequest;

import java.io.File;
import java.io.IOException;

public class UploadObject {

    public static void main(String[] args) throws IOException {
        Regions clientRegion = Regions.DEFAULT_REGION;
        String bucketName = "*** Bucket name ***";
        String stringObjKeyName = "*** String object key name ***";
        String fileObjKeyName = "*** File object key name ***";
        String fileName = "*** Path to file to upload ***";

        try {
            //This code expects that you have AWS credentials set up per:
            // https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .withRegion(clientRegion)
                    .build();

            // Upload a text string as a new object.
            s3Client.putObject(bucketName, stringObjKeyName, "Uploaded String Object");

            // Upload a file as a new object with ContentType and title specified.
            PutObjectRequest request = new PutObjectRequest(bucketName, fileObjKeyName, new File(fileName));
            ObjectMetadata metadata = new ObjectMetadata();
            metadata.setContentType("plain/text");
            metadata.addUserMetadata("title", "someTitle");
            request.setMetadata(metadata);
            s3Client.putObject(request);
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
#### [ JavaScript ]

The following example upload an existing file to an Amazon S3 bucket in a specific Region\.

```
// Import required AWS SDK clients and commands for Node.js.
import { PutObjectCommand } from "@aws-sdk/client-s3";
import { s3Client } from "./libs/s3Client.js"; // Helper function that creates an Amazon S3 service client module.
import {path} from "path";
import {fs} from "fs";

const file = "OBJECT_PATH_AND_NAME"; // Path to and name of object. For example '../myFiles/index.js'.
const fileStream = fs.createReadStream(file);

// Set the parameters
export const uploadParams = {
  Bucket: "BUCKET_NAME",
  // Add the required 'Key' parameter using the 'path' module.
  Key: path.basename(file),
  // Add the required 'Body' parameter
  Body: fileStream,
};


// Upload file to specified bucket.
export const run = async () => {
  try {
    const data = await s3Client.send(new PutObjectCommand(uploadParams));
    console.log("Success", data);
    return data; // For unit tests.
  } catch (err) {
    console.log("Error", err);
  }
};
run();
```

------
#### [ PHP ]

 This topic guides you through using classes from the AWS SDK for PHP to upload an object of up to 5 GB in size\. For larger files, you must use multipart upload API\. For more information, see [Uploading and copying objects using multipart upload](mpuoverview.md)\.

 This topic assumes that you are already following the instructions for [Using the AWS SDK for PHP and Running PHP Examples](UsingTheMPphpAPI.md) and have the AWS SDK for PHP properly installed\.

**Example — Creating an object in an Amazon S3 bucket by uploading data**  
The following PHP example creates an object in a specified bucket by uploading data using the `putObject()` method\. For information about running the PHP examples in this guide, see [Running PHP Examples](UsingTheMPphpAPI.md#running-php-samples)\.   

```
 require 'vendor/autoload.php';

use Aws\S3\S3Client;
use Aws\S3\Exception\S3Exception;

$bucket = '*** Your Bucket Name ***';
$keyname = '*** Your Object Key ***';
                        
$s3 = new S3Client([
    'version' => 'latest',
    'region'  => 'us-east-1'
]);

try {
    // Upload data.
    $result = $s3->putObject([
        'Bucket' => $bucket,
        'Key'    => $keyname,
        'Body'   => 'Hello, world!',
        'ACL'    => 'public-read'
    ]);

    // Print the URL to the object.
    echo $result['ObjectURL'] . PHP_EOL;
} catch (S3Exception $e) {
    echo $e->getMessage() . PHP_EOL;
}
```

------
#### [ Ruby ]

The AWS SDK for Ruby \- Version 3 has two ways of uploading an object to Amazon S3\. The first uses a managed file uploader, which makes it easy to upload files of any size from disk\. To use the managed file uploader method:

1. Create an instance of the `Aws::S3::Resource` class\.

1. Reference the target object by bucket name and key\. Objects live in a bucket and have unique keys that identify each object\.

1. Call`#upload_file` on the object\.

**Example**  

```
require "aws-sdk-s3"

# Wraps Amazon S3 object actions.
class ObjectUploadFileWrapper
  attr_reader :object

  # @param object [Aws::S3::Object] An existing Amazon S3 object.
  def initialize(object)
    @object = object
  end

  # Uploads a file to an Amazon S3 object by using a managed uploader.
  #
  # @param file_path [String] The path to the file to upload.
  # @return [Boolean] True when the file is uploaded; otherwise false.
  def upload_file(file_path)
    @object.upload_file(file_path)
    true
  rescue Aws::Errors::ServiceError => e
    puts "Couldn't upload file #{file_path} to #{@object.key}. Here's why: #{e.message}"
    false
  end
end

def run_demo
  bucket_name = "doc-example-bucket"
  object_key = "my-uploaded-file"
  file_path = "object_upload_file.rb"

  wrapper = ObjectUploadFileWrapper.new(Aws::S3::Object.new(bucket_name, object_key))
  return unless wrapper.upload_file(file_path)

  puts "File #{file_path} successfully uploaded to #{bucket_name}:#{object_key}."
end

run_demo if $PROGRAM_NAME == __FILE__
```

The second way that AWS SDK for Ruby \- Version 3 can upload an object uses the `#put` method of `Aws::S3::Object`\. This is useful if the object is a string or an I/O object that is not a file on disk\. To use this method:

1. Create an instance of the `Aws::S3::Resource` class\.

1. Reference the target object by bucket name and key\.

1. Call`#put`, passing in the string or I/O object\.

**Example**  

```
require "aws-sdk-s3"

# Wraps Amazon S3 object actions.
class ObjectPutWrapper
  attr_reader :object

  # @param object [Aws::S3::Object] An existing Amazon S3 object.
  def initialize(object)
    @object = object
  end

  def put_object(source_file_path)
    File.open(source_file_path, "rb") do |file|
      @object.put(body: file)
    end
    true
  rescue Aws::Errors::ServiceError => e
    puts "Couldn't put #{source_file_path} to #{object.key}. Here's why: #{e.message}"
    false
  end
end

def run_demo
  bucket_name = "doc-example-bucket"
  object_key = "my-object-key"
  file_path = "my-local-file.txt"

  wrapper = ObjectPutWrapper.new(Aws::S3::Object.new(bucket_name, object_key))
  success = wrapper.put_object(file_path)
  return unless success

  puts "Put file #{file_path} into #{object_key} in #{bucket_name}."
end

run_demo if $PROGRAM_NAME == __FILE__
```

------

## Using the REST API<a name="UploadObjSingleOpREST"></a>

You can send REST requests to upload an object\. You can send a PUT request to upload data in a single operation\. For more information, see [PUT Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPUT.html)\.

## Using the AWS CLI<a name="UploadObjSingleOpCLI"></a>

You can send a `PUT` request to upload an object of up to 5 GB in a single operation\. For more information, see the [https://docs.aws.amazon.com/cli/latest/reference/s3api/put-object.html#examples](https://docs.aws.amazon.com/cli/latest/reference/s3api/put-object.html#examples) example in the *AWS CLI Command Reference*\.