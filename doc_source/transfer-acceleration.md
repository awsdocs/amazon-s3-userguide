--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Configuring fast, secure file transfers using Amazon S3 Transfer Acceleration<a name="transfer-acceleration"></a>

Amazon S3 Transfer Acceleration is a bucket\-level feature that enables fast, easy, and secure transfers of files over long distances between your client and an S3 bucket\. Transfer Acceleration takes advantage of the globally distributed edge locations in Amazon CloudFront\. As the data arrives at an edge location, the data is routed to Amazon S3 over an optimized network path\.

When you use Transfer Acceleration, additional data transfer charges might apply\. For more information about pricing, see [Amazon S3 pricing](https://aws.amazon.com/s3/pricing/)\.

## Why use Transfer Acceleration?<a name="transfer-acceleration-why-use"></a>

You might want to use Transfer Acceleration on a bucket for various reasons:
+ Your customers upload to a centralized bucket from all over the world\.
+ You transfer gigabytes to terabytes of data on a regular basis across continents\.
+ You can't use all of your available bandwidth over the internet when uploading to Amazon S3\.

For more information about when to use Transfer Acceleration, see [Amazon S3 FAQs](https://aws.amazon.com/s3/faqs/#s3ta)\.

## Requirements for using Transfer Acceleration<a name="transfer-acceleration-requirements"></a>

The following are required when you are using Transfer Acceleration on an S3 bucket:
+ Transfer Acceleration is only supported on virtual\-hosted style requests\. For more information about virtual\-hosted style requests, see [Making requests using the REST API](RESTAPI.md)\. 
+ The name of the bucket used for Transfer Acceleration must be DNS\-compliant and must not contain periods \("\."\)\.
+ Transfer Acceleration must be enabled on the bucket\. For more information, see [Enabling and using S3 Transfer Acceleration](transfer-acceleration-examples.md)\. 

  After you enable Transfer Acceleration on a bucket, it might take up to 20 minutes before the data transfer speed to the bucket increases\.
**Note**  
Transfer Acceleration is currently not supported for buckets located in the following Regions:  
Africa \(Cape Town\) \(af\-south\-1\)
Asia Pacific \(Hong Kong\) \(ap\-east\-1\)
Asia Pacific \(Osaka\-Local\) \(ap\-northeast\-3\)
Europe \(Stockholm\) \(eu\-north\-1\)
Europe \(Milan\) \(eu\-south\-1\)
Middle East \(Bahrain\) \(me\-south\-1\)
+ To access the bucket that is enabled for Transfer Acceleration, you must use the endpoint `bucketname.s3-accelerate.amazonaws.com`\. Or, use the dual\-stack endpoint `bucketname.s3-accelerate.dualstack.amazonaws.com` to connect to the enabled bucket over IPv6\. 
+ You must be the bucket owner to set the transfer acceleration state\. The bucket owner can assign permissions to other users to allow them to set the acceleration state on a bucket\. The `s3:PutAccelerateConfiguration` permission permits users to enable or disable Transfer Acceleration on a bucket\. The `s3:GetAccelerateConfiguration` permission permits users to return the Transfer Acceleration state of a bucket, which is either `Enabled` or `Suspended.` For more information about these permissions, see [Example — Bucket subresource operations](using-with-s3-actions.md#using-with-s3-actions-related-to-bucket-subresources) and [Identity and access management in Amazon S3](s3-access-control.md)\.

The following sections describe how to get started and use Amazon S3 Transfer Acceleration for transferring data\.

**Topics**
+ [Why use Transfer Acceleration?](#transfer-acceleration-why-use)
+ [Requirements for using Transfer Acceleration](#transfer-acceleration-requirements)
+ [Getting started with Amazon S3 Transfer Acceleration](transfer-acceleration-getting-started.md)
+ [Enabling and using S3 Transfer Acceleration](transfer-acceleration-examples.md)