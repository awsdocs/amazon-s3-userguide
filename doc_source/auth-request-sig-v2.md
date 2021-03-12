--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Appendix b: Authenticating requests \(AWS signature version 2\)<a name="auth-request-sig-v2"></a>

**Important**  
This section describes how to authenticate requests using AWS Signature Version 2\. Signature Version 2 is being turned off \(deprecated\),  Amazon S3 will only accept API requests that are signed using Signature Version 4\. For more information, see [AWS Signature Version 2 Turned Off \(Deprecated\) for Amazon S3](UsingAWSSDK.md#UsingAWSSDK-sig2-deprecation)   
Signature Version 4 is supported in all AWS Regions, and it is the only version that is supported for new Regions\. For more information, see [Authenticating Requests \(AWS Signature Version 4\)](https://docs.aws.amazon.com/AmazonS3/latest/API/sig-v4-authenticating-requests.html) in the *Amazon Simple Storage Service API Reference*\.   
Amazon S3 offers you the ability to identify what API signature version was used to sign a request\. It is important to identify if any of your workflows are utilizing Signature Version 2 signing and upgrading them to use Signature Version 4 to prevent impact to your business\.   
If you are using CloudTrail event logs\(recommended option\), please see [Identifying Amazon S3 Signature Version 2 requests using CloudTrail](cloudtrail-request-identification.md#cloudtrail-identification-sigv2-requests) on how to query and identify such requests\. 
If you are using the Amazon S3 Server Access logs, see [Identifying Signature Version 2 requests using Amazon S3 access logs](using-s3-access-logs-to-identify-requests.md#using-s3-access-logs-to-identify-sigv2-requests) 

**Topics**
+ [Authenticating requests using the REST API](S3_Authentication2.md)
+ [Signing and authenticating REST requests](RESTAuthentication.md)
+ [Browser\-based uploads using POST \(AWS signature version 2\)](UsingHTTPPOST.md)