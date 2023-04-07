# Getting Amazon S3 request IDs for AWS Support<a name="get-request-ids"></a>

Whenever you contact AWS Support because you've encountered errors or unexpected behavior in Amazon S3, you must provide the request IDs associated with the failed action\. AWS Support uses these request IDs to help resolve the problems that you're experiencing\. 

Request IDs come in pairs, are returned in every response that Amazon S3 processes \(even the erroneous ones\), and can be accessed through verbose logs\. There are a number of common methods for getting your request IDs, including S3 access logs and AWS CloudTrail events or data events\.

After you've recovered these logs, copy and retain those two values, because you'll need them when you contact AWS Support\. For information about contacting AWS Support, see [Contact AWS](https://aws.amazon.com/contact-us/) or the [AWS Support Documentation](https://aws.amazon.com/documentation/aws-support/)\.

## Using HTTP to obtain request IDs<a name="http-request-id"></a>

You can obtain your request IDs, `x-amz-request-id` and `x-amz-id-2` by logging the bits of an HTTP request before it reaches the target application\. There are a variety of third\-party tools that can be used to recover verbose logs for HTTP requests\. Choose one that you trust, and then run the tool to listen on the port that your Amazon S3 traffic travels on, as you send out another Amazon S3 HTTP request\.

For HTTP requests, the pair of request IDs will look like the following:

```
x-amz-request-id: 79104EXAMPLEB723 
x-amz-id-2: IOWQ4fDEXAMPLEQM+ey7N9WgVhSnQ6JEXAMPLEZb7hSQDASK+Jd1vEXAMPLEa3Km
```

**Note**  
HTTPS requests are encrypted and hidden in most packet captures\.

## Using a web browser to obtain request IDs<a name="browser-request-id"></a>

Most web browsers have developer tools that you can use to view request headers\.

For web browser\-based requests that return an error, the pair of requests IDs will look like the following examples\.

```
<Error><Code>AccessDenied</Code><Message>Access Denied</Message>
<RequestId>79104EXAMPLEB723</RequestId><HostId>IOWQ4fDEXAMPLEQM+ey7N9WgVhSnQ6JEXAMPLEZb7hSQDASK+Jd1vEXAMPLEa3Km</HostId></Error>
```

To obtain the request ID pair from successful requests, use your browser's developer tools to look at the HTTP response headers\. For information about developer tools for specific browsers, see *Amazon S3 Troubleshooting \- How to recover your S3 request IDs* in [AWS re:Post](https://repost.aws/)\.

## Using the AWS SDKs to obtain request IDs<a name="sdk-request-ids"></a>

The following sections include information for configuring logging by using an AWS SDK\. Although you can enable verbose logging on every request and response, we don't recommend enabling logging in production systems, because large requests or responses can significantly slow down an application\.

For AWS SDK requests, the pair of request IDs will look like the following examples\.

```
Status Code: 403, AWS Service: Amazon S3, AWS Request ID: 79104EXAMPLEB723  
AWS Error Code: AccessDenied  AWS Error Message: Access Denied  
S3 Extended Request ID: IOWQ4fDEXAMPLEQM+ey7N9WgVhSnQ6JEXAMPLEZb7hSQDASK+Jd1vEXAMPLEa3Km
```

### Using the SDK for PHP to obtain request IDs<a name="php-request-id"></a>

You can configure logging by using PHP\. For more information, see [How can I see what data is sent over the wire?](https://docs.aws.amazon.com/aws-sdk-php/guide/latest/faq.html#how-can-i-see-what-data-is-sent-over-the-wire) in the *AWS SDK for PHP Developer Guide*\.

### Using the SDK for Java to obtain request IDs<a name="java-request-id"></a>

You can enable logging for specific requests or responses to catch and return only the relevant headers\. To do this, import the `com.amazonaws.services.s3.S3ResponseMetadata` class\. Afterward, you can store the request in a variable before performing the actual request\. To get the logged request or response, call `getCachedResponseMetadata(AmazonWebServiceRequest request).getRequestID()`\.

**Example**  

```
PutObjectRequest req = new PutObjectRequest(bucketName, key, createSampleFile());
s3.putObject(req);
S3ResponseMetadata md = s3.getCachedResponseMetadata(req);
System.out.println("Host ID: " + md.getHostId() + " RequestID: " + md.getRequestId());
```

Alternatively, you can use verbose logging of every Java request and response\. For more information, see [Verbose Wire Logging](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/java-dg-logging.html#sdk-net-logging-verbose) in the *AWS SDK for Java Developer Guide*\.

### Using the AWS SDK for \.NET to obtain request IDs<a name="net-request-id"></a>

You can configure logging with the AWS SDK for \.NET by using the built\-in `System.Diagnostics` logging tool\. For more information, see the [ Logging with the AWS SDK for \.NET](https://aws.amazon.com/blogs/developer/logging-with-the-aws-sdk-for-net/) *AWS Developer Blog* post\.

**Note**  
By default, the returned log contains only error information\. To get the request IDs, the config file must have `AWSLogMetrics` \(and optionally, `AWSResponseLogging`\) added\.

### Using the SDK for Python \(Boto3\) to obtain request IDs<a name="python-request-id"></a>

With the AWS SDK for Python \(Boto3\), you can log specific responses\. You can use this feature to capture only the relevant headers\. The following code shows how to log parts of the response to a file:

```
import logging
import boto3
logging.basicConfig(filename='logfile.txt', level=logging.INFO)
logger = logging.getLogger(__name__)
s3 = boto3.resource('s3')
response = s3.Bucket(bucket_name).Object(object_key).put()
logger.info("HTTPStatusCode: %s", response['ResponseMetadata']['HTTPStatusCode'])
logger.info("RequestId: %s", response['ResponseMetadata']['RequestId'])
logger.info("HostId: %s", response['ResponseMetadata']['HostId'])
logger.info("Date: %s", response['ResponseMetadata']['HTTPHeaders']['date'])
```

You can also catch exceptions and log relevant information when an exception is raised\. For more information, see [Discerning useful information from error responses](https://boto3.amazonaws.com/v1/documentation/api/latest/guide/error-handling.html#discerning-useful-information-from-error-responses) in the *AWS SDK for Python \(Boto\) API Reference*\.

Additionally, you can configure Boto3 to output verbose debugging logs by using the following code:

```
import boto3
boto3.set_stream_logger('', logging.DEBUG)
```

For more information, see [https://boto3.amazonaws.com/v1/documentation/api/latest/reference/core/boto3.html#boto3.set_stream_logger](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/core/boto3.html#boto3.set_stream_logger) in the *AWS SDK for Python \(Boto\) API Reference*\.

### Using the SDK for Ruby to obtain request IDs<a name="ruby-request-id"></a>

You can get your request IDs using the SDK for Ruby Versions 1, 2, or 3\.
+ **Using the SDK for Ruby \- Version 1**– You can enable HTTP wire logging globally with the following line of code\.

  ```
  s3 = AWS::S3.new(:logger => Logger.new($stdout), :http_wire_trace => true)
  ```
+ **Using the SDK for Ruby \- Version 2 or Version 3**– You can enable HTTP wire logging globally with the following line of code\.

  ```
  s3 = Aws::S3::Client.new(:logger => Logger.new($stdout), :http_wire_trace => true)
  ```

For tips on getting wire information from an AWS client, see [Debugging tip: Getting wire trace information from a client](https://docs.aws.amazon.com/sdk-for-ruby/v3/developer-guide/debugging.html)\.

## Using the AWS CLI to obtain request IDs<a name="cli-request-id"></a>

To get your request IDs when using the AWS Command Line Interface \(AWS CLI\), add `--debug` to your command\.

## Using Windows PowerShell to obtain request IDs<a name="powershell-request-id"></a>

For information on recovering logs with Windows PowerShell, see the [Response Logging in AWS Tools for Windows PowerShell](https://aws.amazon.com/blogs/developer/response-logging-in-aws-tools-for-windows-powershell/) \.NET Development blog post\.

## Using AWS CloudTrail data events to obtain request IDs<a name="cloudtrail-request-id"></a>

An Amazon S3 bucket that is configured with CloudTrail data events to log S3 object\-level API operations provides detailed information about actions that are taken by a user, role, or an AWS service in Amazon S3\. You can [identify S3 request IDs by querying CloudTrail events with Athena](https://docs.aws.amazon.com/AmazonS3/latest/userguide/cloudtrail-request-identification.html)\.

## Using S3 server access logging to obtain request IDs<a name="server-access-log-request-id"></a>

An Amazon S3 bucket configured for S3 server access logging provides detailed records for each request that is made to the bucket\. You can identify S3 request IDs by [querying the server access logs using Athena](https://docs.aws.amazon.com/AmazonS3/latest/userguide/using-s3-access-logs-to-identify-requests.html#querying-s3-access-logs-for-requests)\.