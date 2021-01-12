--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Handling REST and SOAP errors<a name="HandlingErrors"></a>

**Topics**
+ [The REST error response](UsingRESTError.md)
+ [The SOAP error response](#UsingSOAPError)
+ [Amazon S3 error best practices](ErrorBestPractices.md)

This section describes REST and SOAP errors and how to handle them\.

**Note**  
 SOAP support over HTTP is deprecated, but it is still available over HTTPS\. New Amazon S3 features will not be supported for SOAP\. We recommend that you use either the REST API or the AWS SDKs\. 

## The SOAP error response<a name="UsingSOAPError"></a>

**Note**  
 SOAP support over HTTP is deprecated, but it is still available over HTTPS\. New Amazon S3 features will not be supported for SOAP\. We recommend that you use either the REST API or the AWS SDKs\. 

In SOAP, an error result is returned to the client as a SOAP fault, with the HTTP response code 500\. If you do not receive a SOAP fault, then your request was successful\. The Amazon S3 SOAP fault code is comprised of a standard SOAP 1\.1 fault code \(either "Server" or "Client"\) concatenated with the Amazon S3\-specific error code\. For example: "Server\.InternalError" or "Client\.NoSuchBucket"\. The SOAP fault string element contains a generic, human readable error message in English\. Finally, the SOAP fault detail element contains miscellaneous information relevant to the error\.

For example, if you attempt to delete the object "Fred", which does not exist, the body of the SOAP response contains a "NoSuchKey" SOAP fault\.

**Example**  

```
1. <soapenv:Body>
2.   <soapenv:Fault>
3.     <Faultcode>soapenv:Client.NoSuchKey</Faultcode>
4.     <Faultstring>The specified key does not exist.</Faultstring>
5.     <Detail>
6.       <Key>Fred</Key>
7.     </Detail>
8.   </soapenv:Fault>
9. </soapenv:Body>
```

For more information about Amazon S3 errors, go to [ErrorCodeList](https://docs.aws.amazon.com/AmazonS3/latest/API/ErrorResponses.html)\.