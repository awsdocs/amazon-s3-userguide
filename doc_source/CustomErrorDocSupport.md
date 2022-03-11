# Configuring a custom error document<a name="CustomErrorDocSupport"></a>

After you configure your bucket as a static website, when an error occurs, Amazon S3 returns an HTML error document\. You can optionally configure your bucket with a custom error document so that Amazon S3 returns that document when an error occurs\. 

**Note**  
Some browsers display their own error message when an error occurs, ignoring the error document that Amazon S3 returns\. For example, when an HTTP 404 Not Found error occurs, Google Chrome might ignore the error document that Amazon S3 returns and display its own error\.

**Topics**
+ [Amazon S3 HTTP response codes](#s3-http-error-codes)
+ [Configuring a custom error document](#custom-error-document)

## Amazon S3 HTTP response codes<a name="s3-http-error-codes"></a>

The following table lists the subset of HTTP response codes that Amazon S3 returns when an error occurs\. 


| HTTP error code | Description | 
| --- | --- | 
| 301 Moved Permanently | When a user sends a request directly to the Amazon S3 website endpoint \(http://s3\-website\.Region\.amazonaws\.com/\), Amazon S3 returns a 301 Moved Permanently response and redirects those requests to https://aws\.amazon\.com/s3/\. | 
| 302 Found |  When Amazon S3 receives a request for a key `x`, `http://bucket-name.s3-website.Region.amazonaws.com/x`, without a trailing slash, it first looks for the object with the key name `x`\. If the object is not found, Amazon S3 determines that the request is for subfolder `x` and redirects the request by adding a slash at the end, and returns **302 Found**\.   | 
| 304 Not Modified |  Amazon S3 uses request headers `If-Modified-Since`, `If-Unmodified-Since`, `If-Match` and/or `If-None-Match` to determine whether the requested object is same as the cached copy held by the client\. If the object is the same, the website endpoint returns a **304 Not Modified** response\.  | 
| 400 Malformed Request |  The website endpoint responds with a **400 Malformed Request** when a user attempts to access a bucket through the incorrect regional endpoint\.   | 
| 403 Forbidden |  The website endpoint responds with a **403 Forbidden** when a user request translates to an object that is not publicly readable\. The object owner must make the object publicly readable using a bucket policy or an ACL\.   | 
| 404 Not Found |  The website endpoint responds with **404 Not Found** for the following reasons: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/CustomErrorDocSupport.html) You can create a custom document that is returned for **404 Not Found**\. Make sure that the document is uploaded to the bucket configured as a website, and that the website hosting configuration is set to use the document\. For information on how Amazon S3 interprets the URL as a request for an object or an index document, see [Configuring an index document](IndexDocumentSupport.md)\.   | 
| 500 Service Error |  The website endpoint responds with a **500 Service Error** when an internal server error occurs\.  | 
| 503 Service Unavailable |  The website endpoint responds with a **503 Service Unavailable** when Amazon S3 determines that you need to reduce your request rate\.   | 

 For each of these errors, Amazon S3 returns a predefined HTML message\. The following is an example HTML message that is returned for a **403 Forbidden** response\.

![\[403 Forbidden error message example\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/WebsiteErrorExample403.png)

## Configuring a custom error document<a name="custom-error-document"></a>

When you configure your bucket as a static website, you can provide a custom error document that contains a user\-friendly error message and additional help\. Amazon S3 returns your custom error document for only the HTTP 4XX class of error codes\. 

To configure a custom error document using the S3 console, follow the steps below\. You can also configure an error document using the REST API, the AWS SDKs, the AWS CLI, or AWS CloudFormation\. For more information, see the following:
+ [PutBucketWebsite](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketWebsite.html) in the *Amazon Simple Storage Service API Reference*
+ [AWS::S3::Bucket WebsiteConfiguration](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-s3-bucket-websiteconfiguration.html) in the *AWS CloudFormation User Guide*
+ [put\-bucket\-website](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/put-bucket-website.html) in the *AWS CLI Command Reference*

When you enable static website hosting for your bucket, you enter the name of the error document \(for example, **404\.html**\)\. After you enable static website hosting for the bucket, you upload an HTML file with this error document name to your bucket\.

**To configure an error document**

1. Create an error document, for example `404.html`\.

1. Save the error document file locally\.

   The error document name is case sensitive and must exactly match the name that you enter when you enable static website hosting\. For example, if you enter `404.html` for the **Error document** name in the **Static website hosting** dialog box, your error document file name must also be `404.html`\.

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want to use to host a static website\.

1. Enable static website hosting for your bucket, and enter the exact name of your error document \(for example, `404.html`\)\. For more information, see [Enabling website hosting](EnableWebsiteHosting.md)\.

   After enabling static website hosting, proceed to step 6\. 

1. To upload the error document to your bucket, do one of the following:
   + Drag and drop the error document file into the console bucket listing\.
   + Choose **Upload**, and follow the prompts to choose and upload the index file\.

   For step\-by\-step instructions, see [Uploading objects](upload-objects.md)\.