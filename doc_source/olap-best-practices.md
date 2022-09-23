# Best practices and guidelines for S3 Object Lambda<a name="olap-best-practices"></a>

When using S3 Object Lambda, follow these best practices and guidelines to optimize operations and performance\.

**Topics**
+ [Working with S3 Object Lambda](#olap-working-with)
+ [AWS services used in connection with S3 Object Lambda](#olap-services)
+ [Working with Range and partNumber GET headers](#olap-managing-range-part)
+ [Working with the AWS CLI and AWS SDKs](#olap-cli-sdk)
+ [Amazon CloudWatch metrics and dimensions for Object Lambda access points](#olap-cloudwatch-metrics-and-dimensions)

## Working with S3 Object Lambda<a name="olap-working-with"></a>

S3 Object Lambda supports only `GetObject` requests\. Any non\-`GET` requests, such as `ListObjects` or `HeadObject`, will not invoke AWS Lambda and will instead return standard, non\-transformed API responses\. You can create a maximum of 1,000 Object Lambda access points per AWS account per Region\. The AWS Lambda function that you use must be in the same AWS account and Region as the Object Lambda access point\.

S3 Object Lambda allows up to 60 seconds to stream a complete response to its caller\. Your function is also subject to AWS Lambda default quotas\. For more information, see [Lambda quotas](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html) in the *AWS Lambda Developer Guide*\. 

When S3 Object Lambda invokes your specified Lambda function, you are responsible for ensuring that any data that is overwritten or deleted from Amazon S3 by your specified Lambda function or application is intended and correct\.

You can use S3 Object Lambda only to perform operations on objects\. You cannot use S3 Object Lambda to perform other Amazon S3 operations, such as modifying or deleting buckets\. For a complete list of S3 operations that support Object Lambda access points see, [Access point compatibility with AWS services](access-points-usage-examples.md#access-points-service-api-support)\.

In addition to this list, Object Lambda access points do not support the [POST Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOST.html), [CopyObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html) \(as the source\), and [SelectObjectContent](https://docs.aws.amazon.com/AmazonS3/latest/API/API_SelectObjectContent.html) operations\.

## AWS services used in connection with S3 Object Lambda<a name="olap-services"></a>

S3 Object Lambda connects Amazon S3, AWS Lambda, and optionally, other AWS services of your choosing to deliver objects relevant to the requesting applications\. All AWS services used with S3 Object Lambda are governed by their respective Service Level Agreements \(SLAs\)\. For example, if any AWS services does not meet its Service Commitment, you are eligible to receive a Service Credit, as documented in the service's SLA\.

## Working with Range and partNumber GET headers<a name="olap-managing-range-part"></a>

When working with large objects, you can use the `Range` HTTP header to download a specified byte\-range from an object\. When you use the `Range` header, your request fetches only the specified portion of the object\. To fetch different byte ranges from within the same object, you can use concurrent connections to Amazon S3\. You can also use the `partNumber` header \(an integer between 1 and 10,000\) to perform a "ranged" `GET` request for the specified part of the object\. For more information, see [GetObject Request Syntax](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html#API_GetObject_RequestSyntax) in the *Amazon Simple Storage Service API Reference*\.

When receiving a `GET` request, S3 Object Lambda invokes your specified Lambda function first\. Therefore, if your `GET` request contains `Range` or `partNumber` parameters, you must ensure that your Lambda function is equipped to recognize and manage these parameters\. Because there can be multiple entities connected in such a setup \(for example, the requesting client and services such as Lambda, S3, and other services\), we recommend that all involved entities interpret the requested `Range` or `partNumber` in a uniform manner\. Doing so ensures that the ranges that the application is expecting match the ranges that your Lambda function is processing\. When building a function to handle requests with `Range` headers, test all combinations of response sizes, original object sizes, and request range sizes that your application plans to use\.

By default, Object Lambda access points respond with an HTTP 501 status code to any `GetObject` request that contains a `Range` or `partNumber` parameter, either in the headers or query parameters\. You can confirm that your Lambda function is prepared to handle `Range` or `partNumber` requests by updating your Object Lambda access point configuration through the AWS Management Console or the AWS Command Line Interface \(AWS CLI\)\.

## Working with the AWS CLI and AWS SDKs<a name="olap-cli-sdk"></a>

AWS CLI S3 subcommands \(`cp`, `mv`, and `sync`\) and the use of the AWS SDK for Java `TransferManager` class are not supported for use with S3 Object Lambda\.

## Amazon CloudWatch metrics and dimensions for Object Lambda access points<a name="olap-cloudwatch-metrics-and-dimensions"></a>

When making a request through an Object Lambda access point, Amazon S3 sends metrics and dimensions to Amazon CloudWatch\. For a list of S3 Object Lambda metrics or dimensions, see:
+ [S3 Object Lambda request metrics in CloudWatch](metrics-dimensions.md#olap-cloudwatch-metrics)
+ [S3 Object Lambda request dimensions in CloudWatch](metrics-dimensions.md#olap-dimensions)