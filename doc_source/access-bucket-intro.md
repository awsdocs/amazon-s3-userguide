--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Accessing a bucket<a name="access-bucket-intro"></a>

You can access your bucket using the Amazon S3 console\. Using the console UI, you can perform almost all bucket operations without having to write any code\. 

If you access a bucket programmatically, Amazon S3 supports RESTful architecture in which your buckets and objects are resources, each with a resource URI that uniquely identifies the resource\. 

Amazon S3 supports both virtual\-hosted–style and path\-style URLs to access a bucket\. Because buckets can be accessed using path\-style and virtual\-hosted–style URLs, we recommend that you create buckets with DNS\-compliant bucket names\. For more information, see [Bucket restrictions and limitations](BucketRestrictions.md)\.

**Note**  
Virtual\-hosted\-style and path\-style requests use the S3 dot Region endpoint structure \(`s3.Region`\), for example, `https://my-bucket.s3.us-west-2.amazonaws.com`\. However, some older Amazon S3 Regions also support S3 dash Region endpoints `s3-Region`, for example, `https://my-bucket.s3-us-west-2.amazonaws.com`\. If your bucket is in one of these Regions, you might see `s3-Region` endpoints in your server access logs or AWS CloudTrail logs\. We recommend that you do not use this endpoint structure in your requests\. 

## Virtual\-hosted–style access<a name="virtual-host-style-url-ex"></a>

In a virtual\-hosted–style request, the bucket name is part of the domain name in the URL\.

Amazon S3 virtual\-hosted\-style URLs use the following format\.

```
https://bucket-name.s3.Region.amazonaws.com/key name
```

In this example, `my-bucket` is the bucket name, US West \(Oregon\) is the Region, and `puppy.png` is the key name:

```
https://my-bucket.s3.us-west-2.amazonaws.com/puppy.png
```

For more information about virtual hosted style access, see [Virtual Hosted\-Style Requests](VirtualHosting.md#virtual-hosted-style-access)\.

## Path\-style access<a name="path-style-url-ex"></a>

In Amazon S3, path\-style URLs use the following format\.

```
https://s3.Region.amazonaws.com/bucket-name/key name
```

For example, if you create a bucket named `mybucket` in the US West \(Oregon\) Region, and you want to access the `puppy.jpg` object in that bucket, you can use the following path\-style URL:

```
https://s3.us-west-2.amazonaws.com/mybucket/puppy.jpg
```

 For more information, see [Path\-Style Requests](VirtualHosting.md#path-style-access)\.

**Important**  
Update \(September 23, 2020\) – We have decided to delay the deprecation of path\-style URLs to ensure that customers have the time that they need to transition to virtual hosted\-style URLs\. For more information, see [Amazon S3 Path Deprecation Plan – The Rest of the Story](https://aws.amazon.com/blogs/aws/amazon-s3-path-deprecation-plan-the-rest-of-the-story/) in the *AWS News Blog*\.

## Accessing an S3 bucket over IPv6<a name="accessing-bucket-s3-ipv6"></a>

Amazon S3 has a set of dual\-stack endpoints, which support requests to S3 buckets over both Internet Protocol version 6 \(IPv6\) and IPv4\. For more information, see [Making requests over IPv6](ipv6-access.md)\.

## Accessing a bucket through S3 Access Points<a name="accessing-bucket-through-s3-access-point"></a>

In addition to accessing a bucket directly, you can access a bucket through an access point\. For more information about the S3 Access Points feature, see [Managing data access with Amazon S3 access points ](access-points.md)\.

S3 Access Points only support virtual\-host\-style addressing\. To address a bucket through an access point, use the following format\.

```
https://AccessPointName-AccountId.s3-accesspoint.region.amazonaws.com.
```

**Note**  
If your access point name includes dash \(\-\) characters, include the dashes in the URL and insert another dash before the account ID\. For example, to use an access point named `finance-docs` owned by account `123456789012` in Region `us-west-2`, the appropriate URL would be `https://finance-docs-123456789012.s3-accesspoint.us-west-2.amazonaws.com`\.
S3 Access Points don't support access by HTTP, only secure access by HTTPS\.

## Accessing a bucket using S3://<a name="accessing-a-bucket-using-S3-format"></a>

Some AWS services require specifying an Amazon S3 bucket using `S3://bucket`\. The following example shows the correct format\. Be aware that when using this format, the bucket name does not include the AWS Region\.

```
S3://bucket-name/key-name
```

For example, the following example uses the sample bucket described in the earlier path\-style section\.

```
S3://mybucket/puppy.jpg
```