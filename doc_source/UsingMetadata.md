--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Working with object metadata<a name="UsingMetadata"></a>

You can set object metadata in Amazon S3 at the time you upload the object\. After you upload the object, you cannot modify object metadata\. The only way to modify object metadata is to make a copy of the object and set the metadata\. 

There are two kinds of metadata in Amazon S3: *system\-defined metadata* and *user\-defined metadata*\. The sections below provide more information about system\-defined and user\-defined metadata\. For more information about editing metadata using the Amazon S3 console, see [Editing object metadata in the Amazon S3 console](#add-object-metadata)\.

Each object in Amazon S3 can have a set of key\-value pairs that provide *metadata*, which is additional information about the object\. You can set an object's metadata in Amazon S3 at the time you upload it\. 

After you upload an object programmatically using the REST API, AWS CLI, or AWS SDKs, you cannot modify object metadata\. The only way to modify object metadata is to make a copy of the object and set the metadata\. However, you can use the Amazon S3 console to edit some metadata for existing S3 objects\. For more information, see [Editing object metadata in the Amazon S3 console](#add-object-metadata)\.

The sections below provide more information about system\-defined and user\-defined metadata\. For more information about editing metadata using the Amazon S3 console, see [Editing object metadata in the Amazon S3 console](add-object-metadata.md)\.

## System\-defined object metadata<a name="SysMetadata"></a>

For each object stored in a bucket, Amazon S3 maintains a set of system metadata\. Amazon S3 processes this system metadata as needed\. For example, Amazon S3 maintains object creation date and size metadata and uses this information as part of object management\. 

There are two categories of system metadata: 

1. Metadata such as object creation date is system controlled, where only Amazon S3 can modify the value\. 

1. Other system metadata, such as the storage class configured for the object and whether the object has server\-side encryption enabled, are examples of system metadata whose values you control\. If your bucket is configured as a website, sometimes you might want to redirect a page request to another page or an external URL\. In this case, a webpage is an object in your bucket\. Amazon S3 stores the page redirect value as system metadata whose value you control\. 

   When you create objects, you can configure values of these system metadata items or update the values when you need to\. For more information about storage classes, see [Using Amazon S3 storage classes](storage-class-intro.md)\. 

   For more information about server\-side encryption, see [Protecting data using encryption](UsingEncryption.md)\. 

**Note**  
The PUT request header is limited to 8 KB in size\. Within the PUT request header, the system\-defined metadata is limited to 2 KB in size\. The size of system\-defined metadata is measured by taking the sum of the number of bytes in the US\-ASCII encoding of each key and value\. 

The following table provides a list of system\-defined metadata and whether you can update it\.


| Name | Description | Can user modify the value? | 
| --- | --- | --- | 
| Date | Current date and time\. | No | 
| Content\-Length | Object size in bytes\. | No | 
| Content\-Type | Object type\. | Yes | 
| Last\-Modified |  Object creation date or the last modified date, whichever is the latest\.  | No | 
| Content\-MD5 | The base64\-encoded 128\-bit MD5 digest of the object\. | No | 
| x\-amz\-server\-side\-encryption | Indicates whether server\-side encryption is enabled for the object, and whether that encryption is from the AWS Key Management Service \(AWS KMS\) or from Amazon S3 managed encryption \(SSE\-S3\)\. For more information, see [Protecting data using server\-side encryption](serv-side-encryption.md)\.  | Yes | 
| x\-amz\-version\-id | Object version\. When you enable versioning on a bucket, Amazon S3 assigns a version number to objects added to the bucket\. For more information, see [Using versioning in S3 buckets](Versioning.md)\. | No | 
| x\-amz\-delete\-marker | In a bucket that has versioning enabled, this Boolean marker indicates whether the object is a delete marker\.  | No | 
| x\-amz\-storage\-class | Storage class used for storing the object\. For more information, see [Using Amazon S3 storage classes](storage-class-intro.md)\. | Yes | 
| x\-amz\-website\-redirect\-location |  Redirects requests for the associated object to another object in the same bucket or an external URL\. For more information, see [\(Optional\) Configuring a webpage redirect](how-to-page-redirect.md)\. | Yes | 
| x\-amz\-server\-side\-encryption\-aws\-kms\-key\-id | If x\-amz\-server\-side\-encryption is present and has the value of aws:kms, this indicates the ID of the AWS KMS symmetric customer master key \(CMK\) that was used for the object\. | Yes | 
| x\-amz\-server\-side\-encryption\-customer\-algorithm | Indicates whether server\-side encryption with customer\-provided encryption keys \(SSE\-C\) is enabled\. For more information, see [Protecting data using server\-side encryption with customer\-provided encryption keys \(SSE\-C\)](ServerSideEncryptionCustomerKeys.md)\.  | Yes | 

## User\-defined object metadata<a name="UserMetadata"></a>

When uploading an object, you can also assign metadata to the object\. You provide this optional information as a name\-value \(key\-value\) pair when you send a PUT or POST request to create the object\. When you upload objects using the REST API, the optional user\-defined metadata names must begin with "x\-amz\-meta\-" to distinguish them from other HTTP headers\. When you retrieve the object using the REST API, this prefix is returned\. When you upload objects using the SOAP API, the prefix is not required\. When you retrieve the object using the SOAP API, the prefix is removed, regardless of which API you used to upload the object\. 

**Note**  
 SOAP support over HTTP is deprecated, but it is still available over HTTPS\. New Amazon S3 features will not be supported for SOAP\. We recommend that you use either the REST API or the AWS SDKs\. 

When metadata is retrieved through the REST API, Amazon S3 combines headers that have the same name \(ignoring case\) into a comma\-delimited list\. If some metadata contains unprintable characters, it is not returned\. Instead, the `x-amz-missing-meta` header is returned with a value of the number of unprintable metadata entries\. The `HeadObject` action retrieves metadata from an object without returning the object itself\. This operation is useful if you're only interested in an object's metadata\. To use HEAD, you must have READ access to the object\. For more information, see [HeadObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadObject.html) in the * Amazon Simple Storage Service API Reference*\.

User\-defined metadata is a set of key\-value pairs\. Amazon S3 stores user\-defined metadata keys in lowercase\.

Amazon S3 allows arbitrary Unicode characters in your metadata values\.

To avoid issues around the presentation of these metadata values, you should conform to using US\-ASCII characters when using REST and UTF\-8 when using SOAP or browser\-based uploads via POST\.

When using non US\-ASCII characters in your metadata values, the provided Unicode string is examined for non US\-ASCII characters\. If the string contains only US\-ASCII characters, it is presented as is\. If the string contains non US\-ASCII characters, it is first character\-encoded using UTF\-8 and then encoded into US\-ASCII\.

The following is an example\.

```
PUT /Key HTTP/1.1
Host: awsexamplebucket1.s3.amazonaws.com
x-amz-meta-nonascii: ÄMÄZÕÑ S3

HEAD /Key HTTP/1.1
Host: awsexamplebucket1.s3.amazonaws.com
x-amz-meta-nonascii: =?UTF-8?B?w4PChE3Dg8KEWsODwpXDg8KRIFMz?=

PUT /Key HTTP/1.1
Host: awsexamplebucket1.s3.amazonaws.com
x-amz-meta-ascii: AMAZONS3

HEAD /Key HTTP/1.1
Host: awsexamplebucket1.s3.amazonaws.com
x-amz-meta-ascii: AMAZONS3
```

**Note**  
The PUT request header is limited to 8 KB in size\. Within the PUT request header, the user\-defined metadata is limited to 2 KB in size\. The size of user\-defined metadata is measured by taking the sum of the number of bytes in the UTF\-8 encoding of each key and value\. 

For information about adding metadata to your object after it’s been uploaded, [Editing object metadata in the Amazon S3 console](add-object-metadata.md)\.
