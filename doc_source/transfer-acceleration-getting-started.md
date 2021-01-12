--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Getting started with Amazon S3 Transfer Acceleration<a name="transfer-acceleration-getting-started"></a>

You can use Amazon S3 Transfer Acceleration for fast, easy, and secure transfers of files over long distances between your client and an S3 bucket\. Transfer Acceleration uses the globally distributed edge locations in Amazon CloudFront\. As the data arrives at an edge location, data is routed to Amazon S3 over an optimized network path\.

To get started using Amazon S3 Transfer Acceleration, perform the following steps:

1. **Enable Transfer Acceleration on a bucket** 

   

   You can enable Transfer Acceleration on a bucket any of the following ways:
   + Use the Amazon S3 console\. 
   + Use the REST API [PUT Bucket accelerate](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTaccelerate.html) operation\.
   + Use the AWS CLI and AWS SDKs\. For more information, see [Developing with Amazon S3 using the AWS SDKs, and explorers](UsingAWSSDK.md)\. 

   For more information, see [Enabling and using S3 Transfer Acceleration](transfer-acceleration-examples.md)\.
**Note**  
For your bucket to work with transfer acceleration, the bucket name must conform to DNS naming requirements and must not contain periods \("\."\)\. 

1. **Transfer data to and from the acceleration\-enabled bucket**

   Use one of the following s3\-accelerate endpoint domain names:
   + To access an acceleration\-enabled bucket, use `bucketname.s3-accelerate.amazonaws.com`\. 
   + To access an acceleration\-enabled bucket over IPv6, use `bucketname.s3-accelerate.dualstack.amazonaws.com`\. 

     Amazon S3 dual\-stack endpoints support requests to S3 buckets over IPv6 and IPv4\. The Transfer Acceleration dual\-stack endpoint only uses the virtual hosted\-style type of endpoint name\. For more information, see [Getting started making requests over IPv6](ipv6-access.md#ipv6-access-getting-started) and [Using Amazon S3 dual\-stack endpoints](dual-stack-endpoints.md)\.
**Note**  
You can continue to use the regular endpoint in addition to the accelerate endpoints\.

   You can point your Amazon S3 PUT object and GET object requests to the `s3-accelerate` endpoint domain name after you enable Transfer Acceleration\. For example, suppose that you currently have a REST API application using [PUT Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPUT.html) that uses the hostname `mybucket.s3.us-east-1.amazonaws.com` in the `PUT` request\. To accelerate the `PUT`, you change the hostname in your request to `mybucket.s3-accelerate.amazonaws.com`\. To go back to using the standard upload speed, change the name back to `mybucket.s3.us-east-1.amazonaws.com`\.

   After Transfer Acceleration is enabled, it can take up to 20 minutes for you to realize the performance benefit\. However, the accelerate endpoint is available as soon as you enable Transfer Acceleration\.

   You can use the accelerate endpoint in the AWS CLI, AWS SDKs, and other tools that transfer data to and from Amazon S3\. If you are using the AWS SDKs, some of the supported languages use an accelerate endpoint client configuration flag so you don't need to explicitly set the endpoint for Transfer Acceleration to `bucketname.s3-accelerate.amazonaws.com`\. For examples of how to use an accelerate endpoint client configuration flag, see [Enabling and using S3 Transfer Acceleration](transfer-acceleration-examples.md)\.

You can use all Amazon S3 operations through the transfer acceleration endpoints *except* for the following: 
+ [GET Service \(list buckets\)](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTServiceGET.html)
+ [PUT Bucket \(create bucket\)](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUT.html)
+ [DELETE Bucket](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketDELETE.html)

Also, Amazon S3 Transfer Acceleration does not support cross\-Region copies using [PUT Object \- Copy](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectCOPY.html)\. 