# Working with presigned URLs<a name="using-presigned-url"></a>

You can use presigned URLs to you can grant time\-limited access to objects in Amazon S3 without updating your bucket policy\. A presigned URL can be entered in a browser or used by a program to download an object\. The credentials used by the presigned URL are those of the AWS user who generated the URL\.

You can also use presigned URLs to allow someone to upload a specific object to your Amazon S3 bucket\. This allows an upload without requiring another party to have AWS security credentials or permissions\. If an object with the same key already exists in the bucket as specified in the presigned URL, Amazon S3 replaces the existing object with the uploaded object\.

You can use the presigned URL multiple times, up to the expiration date and time\.

When you create a presigned URL, you must provide your security credentials, and then specify the following: 
+ An Amazon S3 bucket
+ An object key \(if downloading this object will be in your Amazon S3 bucket, if uploading this is the file name to be uploaded\)
+ An HTTP method \(`GET` for downloading objects or `PUT` for uploading\)
+ An expiration time interval

**Topics**
+ [Who can create a presigned URL](#who-presigned-url)
+ [Expiration time for presigned URLs](#PresignedUrl-Expiration)
+ [Limiting presigned URL capabilities](#PresignedUrlUploadObject-LimitCapabilities)
+ [Sharing objects with presigned URLs](ShareObjectPreSignedURL.md)
+ [Uploading objects with presigned URLs](PresignedUrlUploadObject.md)

## Who can create a presigned URL<a name="who-presigned-url"></a>

Anyone with valid security credentials can create a presigned URL\. But for someone to successfully access an object, the presigned URL must be created by someone who has permission to perform the operation that the presigned URL is based upon\.

The following are the types of credentials that you can use to create a presigned URL:
+ **IAM instance profile** – Valid up to 6 hours\.
+ **AWS Security Token Service** – Valid up to 36 hours when signed with permanent credentials, such as the credentials of the AWS account root user or an IAM user\.
+ **IAM user** – Valid up to 7 days when you're using AWS Signature Version 4\.

  To create a presigned URL that's valid for up to 7 days, first delegate IAM user credentials \(the access key and secret key\) to the method you're using to create the presigned URL\.

If you created a presigned URL using a temporary credential, the URL expires when the credential expires, even if the URL was created with a later expiration time\.

## Expiration time for presigned URLs<a name="PresignedUrl-Expiration"></a>

A presigned URL remains valid for the period of time specified when the URL is generated\. If you create a presigned URL with the Amazon S3 console, the expiration time can be set between 1 minute and 12 hours\. If you use the AWS CLI or AWS SDKs, the expiration time can be set as high as 7 days\.

If you created a presigned URL by using a temporary token, then the URL expires when the token expires, even if you created the URL with a later expiration time\. For more information about how the credentials you use affect the expiration time, see [Who can create a presigned URL](#who-presigned-url)\.

Amazon S3 checks the expiration date and time of a signed URL at the time of the HTTP request\. For example, if a client begins to download a large file immediately before the expiration time, the download continues even if the expiration time passes during the download\. However, if the connection drops and the client tries to restart the download after the expiration time passes, the download fails\.

## Limiting presigned URL capabilities<a name="PresignedUrlUploadObject-LimitCapabilities"></a>

The capabilities of a presigned URL are limited by the permissions of the user who created it\. In essence, presigned URLs are bearer tokens that grant access to those who possess them\. As such, we recommend that you protect them appropriately\. The following are some methods that you can use to restrict the use of your presigned URLs\. 

**AWS Signature Version 4 \(SigV4\)**  
To enforce specific behavior when presigned URL requests are authenticated by using AWS Signature Version 4 \(SigV4\), you can use condition keys in bucket policies and access point policies\. For example, the following bucket policy uses the `s3:signatureAge` condition to deny any Amazon S3 presigned URL request on objects in the `DOC-EXAMPLE-BUCKET1` bucket if the signature is more than 10 minutes old\. To use this example, replace the *`user input placeholders`* with your own information\.

```
 1. {
 2.    "Version": "2012-10-17",
 3.    "Statement": [
 4.       {
 5.          "Sid": "Deny a presigned URL request if the signature is more than 10 min old",
 6.          "Effect": "Deny",
 7.          "Principal": {"AWS":"444455556666"},
 8.          "Action": "s3:*",
 9.          "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET1/*",
10.          "Condition": {
11.             "NumericGreaterThan": {
12.                "s3:signatureAge": 600000
13.             }
14.          }
15.       }
16.    ]
17. }
```

For more information about policy keys related AWS Signature Version 4, see [AWS Signature Version 4 Authentication](https://docs.aws.amazon.com/AmazonS3/latest/API/bucket-policy-s3-sigv4-conditions.html) in the *Amazon Simple Storage Service API Reference*\.

**Network path restriction**  
If you want to restrict the use of presigned URLs and all Amazon S3 access to particular network paths, you can write AWS Identity and Access Management \(IAM\) policies\. You can set these policies on the IAM principal that makes the call, the Amazon S3 bucket, or both\. 

A network\-path restriction on the IAM principal requires the user of those credentials to make requests from the specified network\. A restriction on the bucket or access point requires that all requests to that resource originate from the specified network\. These restrictions also apply outside of the presigned URL scenario\.

The IAM global condition key that you use depends on the type of endpoint\. If you're using the public endpoint for Amazon S3, use `aws:SourceIp`\. If you're using a virtual private cloud \(VPC\) endpoint to Amazon S3, use `aws:SourceVpc` or `aws:SourceVpce`\.

The following IAM policy statement requires the principal to access AWS only from the specified network range\. With this policy statement, all access must originate from that range\. This includes the case of someone who's using a presigned URL for Amazon S3\. To use this example, replace the *`user input placeholders`* with your own information\.

```
{
    "Sid": "NetworkRestrictionForIAMPrincipal",
    "Effect": "Deny",
    "Action": "*",
    "Resource": "*",
    "Condition": {
        "NotIpAddressIfExists": {"aws:SourceIp": "IP-address-range"},
        "BoolIfExists": {"aws:ViaAWSService": "false"}
    }
}
```

For additional example bucket policies that use the `aws:SourceIp` AWS global condition key to restrict access to an Amazon S3 bucket to a specific network range, see [Managing access based on specific IP addresses](example-bucket-policies.md#example-bucket-policies-IP)\.