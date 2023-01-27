# Using server\-side encryption with Amazon S3\-managed encryption keys \(SSE\-S3\)<a name="UsingServerSideEncryption"></a>

**Important**  
Amazon S3 now applies server\-side encryption with Amazon S3 managed keys \(SSE\-S3\) as the base level of encryption for every bucket in Amazon S3\. Starting January 5, 2023, all new object uploads to Amazon S3 will be automatically encrypted at no additional cost and with no impact on performance\. Currently, the automatic encryption status for S3 bucket default encryption configuration and for new object uploads is available in AWS CloudTrail logs, S3 Inventory, and S3 Storage Lens\. During the next few weeks, the automatic encryption status will also be rolled out to the Amazon S3 console and as an additional Amazon S3 API response header in the AWS Command Line Interface and AWS SDKs\. When this update is complete in all AWS Regions, we will update the documentation\. For more information, see [Default encryption FAQ](https://docs.aws.amazon.com/AmazonS3/latest/userguide/default-encryption-faq.html)\.

Server\-side encryption protects data at rest\. Amazon S3 encrypts each object with a unique key\. As an additional safeguard, it encrypts the key itself with a key that it rotates regularly\. Amazon S3 server\-side encryption uses one of the strongest block ciphers available to encrypt your data, 256\-bit Advanced Encryption Standard \(AES\-256\)\.

There are no additional fees for using server\-side encryption with Amazon S3\-managed keys \(SSE\-S3\)\. However, requests to configure the default encryption feature incur standard Amazon S3 request charges\. For information about pricing, see [Amazon S3 pricing](http://aws.amazon.com/s3/pricing/)\.

If you need server\-side encryption for all of the objects that are stored in a bucket, use a bucket policy\. For example, the following bucket policy denies permissions to upload an object unless the request includes the `x-amz-server-side-encryption` header to request server\-side encryption:

```
 1. {
 2.   "Version": "2012-10-17",
 3.   "Id": "PutObjectPolicy",
 4.   "Statement": [
 5.     {
 6.       "Sid": "DenyIncorrectEncryptionHeader",
 7.       "Effect": "Deny",
 8.       "Principal": "*",
 9.       "Action": "s3:PutObject",
10.       "Resource": "arn:aws:s3:::awsexamplebucket1/*",
11.       "Condition": {
12.         "StringNotEquals": {
13.           "s3:x-amz-server-side-encryption": "AES256"
14.         }
15.       }
16.     }
17.   ]
18. }
```

**Note**  
Server\-side encryption encrypts only the object data, not object metadata\. 

## API Support for Server\-Side Encryption<a name="APISupportforServer-SideEncryption"></a>

To request server\-side encryption using the object creation REST APIs, provide the `x-amz-server-side-encryption` request header\. For information about the REST APIs, see [Using the REST API](specifying-s3-encryption.md#SSEUsingRESTAPI)\.

The following Amazon S3 APIs support this header:
+ PUT operations—Specify the request header when uploading data using the PUT API\. For more information, see [PUT Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPUT.html)\.
+ Initiate Multipart Upload—Specify the header in the initiate request when uploading large objects using the multipart upload API\. For more information, see [Initiate Multipart Upload](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadInitiate.html)\.
+ COPY operations—When you copy an object, you have both a source object and a target object\. For more information, see [PUT Object \- Copy](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectCOPY.html)\.

**Note**  
When using a POST operation to upload an object, instead of providing the request header, you provide the same information in the form fields\. For more information, see [POST Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOST.html)\. 

The AWS SDKs also provide wrapper APIs that you can use to request server\-side encryption\. You can also use the AWS Management Console to upload objects and request server\-side encryption\.

**Topics**
+ [API Support for Server\-Side Encryption](#APISupportforServer-SideEncryption)
+ [Specifying Amazon S3 encryption](specifying-s3-encryption.md)