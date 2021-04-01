--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Best practices and guidelines for S3 Object Lambda<a name="olap-best-practices"></a>

When using S3 Object Lambda, follow these best practices and guidelines to optimize operations and performance\.

**Topics**
+ [Working with S3 Object Lambda](#olap-working-with)
+ [AWS Services used in connection with S3 Object Lambda](#olap-services)
+ [Working with Range and partNumber GET headers](#olap-managing-range-part)
+ [Working with AWS CLI and SDKs](#olap-cli-sdk)

## Working with S3 Object Lambda<a name="olap-working-with"></a>

S3 Object Lambda only support processing GET requests\. Any non\-GET requests, such as LIST or HEAD, will not invoke Lambda and return standard, non\-transformed API responses\. You can create a maximum of 1,000 Object Lambda Access Points per AWS account per Region\. The AWS Lambda function that you use must be in the same AWS account and Region as the Object Lambda Access Point\.

S3 Object Lambda allows up to 60 seconds to stream a complete response to its caller\. Your function is also subject to Lambda default quotas\. For more information, see [Lambda quotas](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-limits.html) in the *AWS Lambda Developer Guide*\. Using S3 Object Lambda invokes your specified Lambda function and you are responsible for ensuring that any data overwritten or deleted from S3 by your specified Lambda function or application is intended and correct\.

You can only use S3 Object Lambda to perform operations on objects\. You cannot use them to perform other Amazon S3 operations, such as modifying or deleting buckets\. For a complete list of S3 operations that support access points see, [Access point compatibility with S3 operations and AWS services](access-points-usage-examples.md#access-points-service-api-support)\.

In addition to this list, S3 Object Lambda access points do not support [POST Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOST.html), [Copy](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html) \(as the source\), or [Select Object Content](https://docs.aws.amazon.com/AmazonS3/latest/API/API_SelectObjectContent.html)\.

## AWS Services used in connection with S3 Object Lambda<a name="olap-services"></a>

S3 Object Lambda connects Amazon S3, AWS Lambda, and optionally, other AWS services of your choosing to deliver objects relevant to requesting applications\. All AWS services used in connection with S3 Object Lambda will continue to be governed by their respective Service Level Agreements \(SLA\)\. For example, in the event that any AWS service does not meet its Service Commitment, you will be eligible to receive a Service Credit as documented in the service’s SLA\.

## Working with Range and partNumber GET headers<a name="olap-managing-range-part"></a>

When working with large objects you can use the Range HTTP header to download a specified byte\-range from an object fetching only the specified portion\. You can use concurrent connections to Amazon S3 to fetch different byte ranges from within the same object\. You can also use partNumber \(integer between 1 and 10,000\) which effectively performs a ‘ranged’ GET request for the specified part from the object\. For more information, see [GetObject Request Syntax](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html#API_GetObject_RequestSyntax) in the Amazon Simple Storage Service API Reference\.

When receiving a GET request, S3 Object Lambda invokes your specified Lambda function first, hence if your GET request contains range or part number parameters, you must ensure that your Lambda function is equipped to recognize and manage these parameters\. Because there can be multiple entities connected in such a setup \(requesting client and services like Lambda, S3, other\) it is advised that all involved entities interpret the requested range \(or partNumber\) in a uniform manner\. This ensures that the ranges the application is expecting match with the ranges your Lambda function is processing\. When building a function to handle requests with range headers test all combinations of response sizes, original object sizes, and request range sizes that your application plans to use\.

By default, S3 Object Lambda access points will respond with a 501 to any GetObject request that contains a range or part number parameter, either in the headers or query parameters\. You can confirm that your Lambda function is prepared to handle range or part requests by updating your Object Lambda access point configuration through the AWS Management Console or the AWS CLI\.

## Working with AWS CLI and SDKs<a name="olap-cli-sdk"></a>

AWS CLI S3 subcommands \(cp, mv and sync\) and use of Transfer Manager is not supported in conjunction with S3 Object Lambda\.