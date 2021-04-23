# Listing multipart uploads<a name="list-mpu"></a>

You can use the AWS SDKs \(low\-level API\) to retrieve a list of in\-progress multipart uploads in Amazon S3\.

## Listing multipart uploads using the AWS SDK \(low\-level API\)<a name="list-aws-sdk"></a>

------
#### [ Java ]

The following tasks guide you through using the low\-level Java classes to list all in\-progress multipart uploads on a bucket\.


**Low\-level API multipart uploads listing process**  

|  |  | 
| --- |--- |
| 1 | Create an instance of the `ListMultipartUploadsRequest` class and provide the bucket name\. | 
| 2 | Run the `AmazonS3Client.listMultipartUploads` method\. The method returns an instance of the `MultipartUploadListing` class that gives you information about the multipart uploads in progress\. | 

The following Java code example demonstrates the preceding tasks\.

**Example**  

```
1. ListMultipartUploadsRequest allMultpartUploadsRequest = 
2.      new ListMultipartUploadsRequest(existingBucketName);
3. MultipartUploadListing multipartUploadListing = 
4.      s3Client.listMultipartUploads(allMultpartUploadsRequest);
```

------
#### [ \.NET ]

To list all of the in\-progress multipart uploads on a specific bucket, use the AWS SDK for \.NET low\-level multipart upload API's `ListMultipartUploadsRequest` class\. The `AmazonS3Client.ListMultipartUploads` method returns an instance of the `ListMultipartUploadsResponse` class that provides information about the in\-progress multipart uploads\. 

An in\-progress multipart upload is a multipart upload that has been initiated using the initiate multipart upload request, but has not yet been completed or stopped\. For more information about Amazon S3 multipart uploads, see [Uploading and copying objects using multipart upload](mpuoverview.md)\.

The following C\# example shows how to use the AWS SDK for \.NET to list all in\-progress multipart uploads on a bucket\. For information about the example's compatibility with a specific version of the AWS SDK for \.NET and instructions on how to create and test a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\.

```
ListMultipartUploadsRequest request = new ListMultipartUploadsRequest
{
	 BucketName = bucketName // Bucket receiving the uploads.
};

ListMultipartUploadsResponse response = await AmazonS3Client.ListMultipartUploadsAsync(request);
```

------
#### [ PHP ]

This topic shows how to use the low\-level API classes from version 3 of the AWS SDK for PHP to list all in\-progress multipart uploads on a bucket\. It assumes that you are already following the instructions for [Using the AWS SDK for PHP and Running PHP Examples](UsingTheMPphpAPI.md) and have the AWS SDK for PHP properly installed\. 

The following PHP example demonstrates listing all in\-progress multipart uploads on a bucket\.

```
 require 'vendor/autoload.php';

use Aws\S3\S3Client;

$bucket = '*** Your Bucket Name ***';

$s3 = new S3Client([
    'version' => 'latest',
    'region'  => 'us-east-1'
]);

// Retrieve a list of the current multipart uploads.
$result = $s3->listMultipartUploads([
    'Bucket' => $bucket
]);

// Write the list of uploads to the page.
print_r($result->toArray());
```

------

## Listing multipart uploads using the AWS CLI<a name="list-mpu-cli"></a>

The following sections in the AWS Command Line Interface describe the operations for listing multipart uploads\. 
+ [list\-parts](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/list-parts.html)‐list the uploaded parts for a specific multipart upload\.
+ [list\-multipart\-uploads](https://docs.aws.amazon.com/cli/latest/reference/s3api/list-multipart-uploads.html)‐list in\-progress multipart uploads\.