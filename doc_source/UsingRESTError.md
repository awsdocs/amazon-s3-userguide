# The REST error response<a name="UsingRESTError"></a>

If a REST request results in an error, the HTTP reply has: 
+ An XML error document as the response body 
+ Content\-Type: application/xml
+ An appropriate 3xx, 4xx, or 5xx HTTP status code

Following is an example of a REST Error Response\.

```
1. <?xml version="1.0" encoding="UTF-8"?>
2. <Error>
3.   <Code>NoSuchKey</Code>
4.   <Message>The resource you requested does not exist</Message>
5.   <Resource>/mybucket/myfoto.jpg</Resource> 
6.   <RequestId>4442587FB7D0A2F9</RequestId>
7. </Error>
```

For more information about Amazon S3 errors, go to [ErrorCodeList](https://docs.aws.amazon.com/AmazonS3/latest/API/ErrorResponses.html)\.

## Response headers<a name="UsingRESTErrorResponseHeaders"></a>

Following are response headers returned by all operations:
+ `x-amz-request-id:` A unique ID assigned to each request by the system\. In the unlikely event that you have problems with Amazon S3, Amazon can use this to help troubleshoot the problem\.
+ `x-amz-id-2:` A special token that will help us to troubleshoot problems\.

## Error response<a name="ErrorResponse"></a>

When an Amazon S3 request is in error, the client receives an error response\. The exact format of the error response is API specific: For example, the REST error response differs from the SOAP error response\. However, all error responses have common elements\.

**Note**  
 SOAP support over HTTP is deprecated, but SOAP is still available over HTTPS\. New Amazon S3 features are not supported for SOAP\. Instead of using SOAP, we recommend that you use either the REST API or the AWS SDKs\. 

### Error code<a name="ErrorCode"></a>

The error code is a string that uniquely identifies an error condition\. It is meant to be read and understood by programs that detect and handle errors by type\. Many error codes are common across SOAP and REST APIs, but some are API\-specific\. For example, NoSuchKey is universal, but UnexpectedContent can occur only in response to an invalid REST request\. In all cases, SOAP fault codes carry a prefix as indicated in the table of error codes, so that a NoSuchKey error is actually returned in SOAP as Client\.NoSuchKey\.

**Note**  
 SOAP support over HTTP is deprecated, but SOAP is still available over HTTPS\. New Amazon S3 features are not supported for SOAP\. Instead of using SOAP, we recommend that you use either the REST API or the AWS SDKs\. 

### Error message<a name="ErrorMessage"></a>

The error message contains a generic description of the error condition in English\. It is intended for a human audience\. Simple programs display the message directly to the end user if they encounter an error condition they don't know how or don't care to handle\. Sophisticated programs with more exhaustive error handling and proper internationalization are more likely to ignore the error message\.

### Further details<a name="ErrorDetails"></a>

Many error responses contain additional structured data meant to be read and understood by a developer diagnosing programming errors\. For example, if you send a Content\-MD5 header with a REST PUT request that doesn't match the digest calculated on the server, you receive a BadDigest error\. The error response also includes as detail elements the digest we calculated, and the digest you told us to expect\. During development, you can use this information to diagnose the error\. In production, a well\-behaved program might include this information in its error log\.