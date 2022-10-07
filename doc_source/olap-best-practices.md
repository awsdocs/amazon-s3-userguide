# Best practices and guidelines for S3 Object Lambda<a name="olap-best-practices"></a>

When using S3 Object Lambda, follow these best practices and guidelines to optimize operations and performance\.

**Topics**
+ [Working with S3 Object Lambda](#olap-working-with)
+ [AWS services used in connection with S3 Object Lambda](#olap-services)
+ [`Range` and `partNumber` headers](#olap-managing-range-part)
+ [Transforming the `expiry-date`](#olap-console-download)
+ [Working with the AWS CLI and AWS SDKs](#olap-cli-sdk)

## Working with S3 Object Lambda<a name="olap-working-with"></a>

S3 Object Lambda supports processing only `GET`, `LIST`, and `HEAD` requests\. Any other requests don't invoke AWS Lambda and instead return standard, non\-transformed API responses\. You can create a maximum of 1,000 Object Lambda access points per AWS account per Region\. The AWS Lambda function that you use must be in the same AWS account and Region as the Object Lambda access point\.

S3 Object Lambda allows up to 60 seconds to stream a complete response to its caller\. Your function is also subject to AWS Lambda default quotas\. For more information, see [Lambda quotas](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html) in the *AWS Lambda Developer Guide*\. 

When S3 Object Lambda invokes your specified Lambda function, you are responsible for ensuring that any data that is overwritten or deleted from Amazon S3 by your specified Lambda function or application is intended and correct\.

You can use S3 Object Lambda only to perform operations on objects\. You cannot use S3 Object Lambda to perform other Amazon S3 operations, such as modifying or deleting buckets\. For a complete list of S3 operations that support access points, see [Access point compatibility with S3 operations](access-points-usage-examples.md#access-points-operations-support)\.

In addition to this list, Object Lambda access points do not support the [https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOST.html](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOST.html), [https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html) \(as the source\), and [https://docs.aws.amazon.com/AmazonS3/latest/API/API_SelectObjectContent.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_SelectObjectContent.html) API operations\.

## AWS services used in connection with S3 Object Lambda<a name="olap-services"></a>

S3 Object Lambda connects Amazon S3, AWS Lambda, and optionally, other AWS services of your choosing to deliver objects relevant to the requesting applications\. All AWS services used with S3 Object Lambda are governed by their respective Service Level Agreements \(SLAs\)\. For example, if any AWS service does not meet its Service Commitment, you are eligible to receive a Service Credit, as documented in the service's SLA\.

## `Range` and `partNumber` headers<a name="olap-managing-range-part"></a>

When working with large objects, you can use the `Range` HTTP header to download a specified byte\-range from an object\. When you use the `Range` header, your request fetches only the specified portion of the object\. You can also use the `partNumber` header to perform a ranged request for the specified part from the object\.

For more information see, [Working with Range and partNumber headers](range-get-olap.md)\.

## Transforming the `expiry-date`<a name="olap-console-download"></a>

You can open or download transformed objects from your Object Lambda access point on the AWS Management Console\. These objects must be non\-expired\. If your Lambda function transforms the `expiry-date` of your objects, you might see expired objects that cannot be opened or downloaded\. This behavior applies only to S3 Glacier Flexible Retrieval and S3 Glacier Deep Archive restored objects\.

## Working with the AWS CLI and AWS SDKs<a name="olap-cli-sdk"></a>

AWS Command Line Interface \(AWS CLI\) S3 subcommands \(`cp`, `mv`, and `sync`\) and the use of the AWS SDK for Java `TransferManager` class are not supported for use with S3 Object Lambda\.