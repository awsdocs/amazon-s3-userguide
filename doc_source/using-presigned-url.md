# Using presigned URLs<a name="using-presigned-url"></a>

All objects and buckets are private by default\. However, you can use a presigned URL to optionally share objects or allow your customers/users to upload objects to buckets without AWS security credentials or permissions\.

You can use presigned URLs to generate a URL that can be used to access your Amazon S3 buckets\. When you create a presigned URL, you associate it with a specific action\. You can share the URL, and anyone with access to it can perform the action embedded in the URL as if they were the original signing user\. The URL will expire and no longer work when it reaches its expiration time\.

**Important**  
For all Regions that launched after March 20, 2019, if a request arrives at the wrong Amazon S3 location, Amazon S3 returns an HTTP 400 Bad Request error\.

## Limiting presigned URL capabilities<a name="PresignedUrlUploadObject-LimitCapabilities"></a>

The capabilities of a presigned URL are limited by the permissions of the user who created it\. In essence, presigned URLs are a bearer token that grants access to those who possess them\. As such, we recommend that you protect them appropriately\.

If you want to restrict the use of presigned URLs and all S3 access to particular network paths, you can write AWS Identity and Access Management \(IAM\) policies that require a particular network path\. You can set these policies on the IAM principal that makes the call, the Amazon S3 bucket, or both\. 

A network\-path restriction on the principal requires the user of those credentials to make requests from the specified network\. A restriction on the bucket limits access to that bucket only to requests that originate from the specified network\. Realize that these restrictions also apply outside of the presigned URL scenario\.

The IAM global condition that you use depends on the type of endpoint\. If you are using the public endpoint for Amazon S3, use `aws:SourceIp`\. If you are using a VPC endpoint to Amazon S3, use `aws:SourceVpc` or `aws:SourceVpce`\.

The following IAM policy statement requires the principal to access AWS only from the specified network range\. With this policy statement in place, all access is required to originate from that range\. This includes the case of someone using a presigned URL for S3\.

```
{
  "Sid": "NetworkRestrictionForIAMPrincipal",
  "Effect": "Deny",
  "Action": "",
  "Resource": "",
  "Condition": {
    "NotIpAddressIfExists": { "aws:SourceIp": "IP-address" },
    "BoolIfExists": { "aws:ViaAWSService": "false" }
  }
}
```

## Who can create a presigned URL<a name="who-presigned-url"></a>

Anyone with valid security credentials can create a presigned URL\. But for someone to successfully access an object, the presigned URL must be created by someone who has permission to perform the operation that the presigned URL is based upon\.

The following are credentials that you can use to create a presigned URL:
+ IAM instance profile: Valid up to 6 hours\.
+ AWS Security Token Service: Valid up to 36 hours when signed with permanent credentials, such as the credentials of the AWS account root user or an IAM user\.
+ IAM user: Valid up to 7 days when using AWS Signature Version 4\.

  To create a presigned URL that's valid for up to 7 days, first designate IAM user credentials \(the access key and secret key\) to the SDK that you're using\. Then, generate a presigned URL using AWS Signature Version 4\.

**Note**  
If you created a presigned URL using a temporary token, the URL expires when the token expires, even if the URL was created with a later expiration time\.
Because presigned URLs grant access to your Amazon S3 buckets to whoever has the URL, we recommend that you protect them appropriately\. For more information about protecting presigned URLs, see [Limiting presigned URL capabilities](#PresignedUrlUploadObject-LimitCapabilities)\.

## When does Amazon S3 check the expiration date and time of a presigned URL?<a name="presigned-url-when-checked"></a>

Amazon S3 checks the expiration date and time of a signed URL at the time of the HTTP request\. For example, if a client begins to download a large file immediately before the expiration time, the download should complete even if the expiration time passes during the download\. If the connection drops and the client tries to restart the download after the expiration time passes, the download will fail\.

For more information about using a presigned URL to share or upload objects, see the following topics\.

**Topics**
+ [Limiting presigned URL capabilities](#PresignedUrlUploadObject-LimitCapabilities)
+ [Who can create a presigned URL](#who-presigned-url)
+ [When does Amazon S3 check the expiration date and time of a presigned URL?](#presigned-url-when-checked)
+ [Sharing objects using presigned URLs](ShareObjectPreSignedURL.md)
+ [Generating a presigned URL to upload an object](PresignedUrlUploadObject.md)
+ [Deleting an object using a presigned URL with the AWS SDK for JavaScript](PresignedUrlDelete.md)