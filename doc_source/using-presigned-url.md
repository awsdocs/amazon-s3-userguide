# Using presigned URLs<a name="using-presigned-url"></a>

All objects and buckets are private by default\. However, you can use a presigned URL to optionally share objects or enable your customers/users to upload objects to buckets without AWS security credentials or permissions\.

## Limiting presigned URL capabilities<a name="PresignedUrlUploadObject-LimitCapabilities"></a>

You can use presigned URLs to generate a URL that can be used to access your S3 buckets\. When you create a presigned URL, you associate it with a specific action\. You can share the URL, and anyone with access to it can perform the action embedded in the URL as if they were the original signing user\. The URL will expire and no longer work when it reaches its expiration time\. The capabilities of the URL are limited by the permissions of the user who created the presigned URL\. 

In essence, presigned URLs are a bearer token that grants access to customers who possess them\. As such, we recommend that you protect them appropriately\.

If you want to restrict the use of presigned URLs and all S3 access to particular network paths, you can write AWS Identity and Access Management \(IAM\) policies that require a particular network path\. These policies can be set on the IAM principal that makes the call, the Amazon S3 bucket, or both\. A network\-path restriction on the principal requires the user of those credentials to make requests from the specified network\. A restriction on the bucket limits access to that bucket only to requests originating from the specified network\. Realize that these restrictions also apply outside of the presigned URL scenario\.

The IAM global condition that you use depends on the type of endpoint\. If you are using the public endpoint for Amazon S3, use `aws:SourceIp`\. If you are using a VPC endpoint to Amazon S3, use `aws:SourceVpc` or `aws:SourceVpce`\.

The following IAM policy statement requires the principal to access AWS from only the specified network range\. With this policy statement in place, all access is required to originate from that range\. This includes the case of someone using a presigned URL for S3\.

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

For more information about using a presigned URL to share or upload objects, see the topics below\.

**Topics**
+ [Limiting presigned URL capabilities](#PresignedUrlUploadObject-LimitCapabilities)
+ [Sharing an object with a presigned URL](ShareObjectPreSignedURL.md)
+ [Uploading objects using presigned URLs](PresignedUrlUploadObject.md)