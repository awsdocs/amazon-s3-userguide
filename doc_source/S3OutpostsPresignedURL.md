# Using presigned URLs for S3 on Outposts<a name="S3OutpostsPresignedURL"></a>

To grant time\-limited access to objects that are stored locally on an Outpost without updating your bucket policy, you can use a presigned URL\. With presigned URLs, you as the bucket owner can share objects with individuals in your virtual private cloud \(VPC\) or grant them the ability to upload or delete objects\. 

When you create a presigned URL by using the AWS SDKs or the AWS Command Line Interface \(AWS CLI\), you associate the URL with a specific action\. You also grant time\-limited access to the presigned URL by choosing a custom expiration time that can be as low as 1 second and as high as 7 days\. When you share the presigned URL, the individual in the VPC can perform the action embedded in the URL as if they were the original signing user\. When the URL reaches its expiration time, the URL expires and no longer works\.

## Limiting presigned URL capabilities<a name="S3OutpostsPresignedUrlUploadObjectLimitCapabilities"></a>

The capabilities of a presigned URL are limited by the permissions of the user who created it\. In essence, presigned URLs are bearer tokens that grant access to those who possess them\. As such, we recommend that you protect them appropriately\. 

**AWS Signature Version 4 \(SigV4\)**  
To enforce specific behavior when presigned URL requests are authenticated by using AWS Signature Version 4 \(SigV4\), you can use condition keys in bucket policies and access point policies\. For example, you can create a bucket policy that uses the `s3-outposts:signatureAge` condition to deny any Amazon S3 on Outposts presigned URL request on objects in the `example-outpost-bucket` bucket if the signature is more than 10 minutes old\. To use this example, replace the *`user input placeholders`* with your own information\.

```
 1. {
 2.     "Version": "2012-10-17",
 3.     "Statement": [
 4.         {
 5.             "Sid": "Deny a presigned URL request if the signature is more than 10 minutes old",
 6.             "Effect": "Deny",
 7.             "Principal": {"AWS":"444455556666"},
 8.             "Action": "s3-outposts:*",
 9.             "Resource": "arn:aws:s3-outposts:us-east-1:111122223333:outpost/op-01ac5d28a6a232904/bucket/example-outpost-bucket/object/*",
10.             "Condition": {
11.                 "NumericGreaterThan": {"s3-outposts:signatureAge": 600000},
12.                 "StringEquals": {"s3-outposts:authType": "REST-QUERY-STRING"}
13.             }
14.         }
15.     ]
16. }
```

For a list of condition keys and additional example policies that you can use to enforce specific behavior when presigned URL requests are authenticated by using Signature Version 4, see [AWS Signature Version 4 \(SigV4\) authentication\-specific policy keys](s3-outposts-bucket-policy-s3-sigv4-conditions.md)\.

**Network path restriction**  
If you want to restrict the use of presigned URLs and all S3 on Outposts access to particular network paths, you can write policies that require a particular network path\. To set the restriction on the IAM principal that makes the call, you can use identity\-based AWS Identity and Access Management \(IAM\) policies \(for example, IAM user, group, or role policies\)\. To set the restriction on the S3 on Outposts resource, you can use resource\-based policies \(for example, bucket and access point policies\)\. 

A network\-path restriction on the IAM principal requires the user of those credentials to make requests from the specified network\. A restriction on the bucket or access point requires that all requests to that resource originate from the specified network\. These restrictions also apply outside of the presigned URL scenario\.

The IAM global condition that you use depends on the type of endpoint\. If you are using the public endpoint for S3 on Outposts, use `aws:SourceIp`\. If you are using a VPC endpoint for S3 on Outposts, use `aws:SourceVpc` or `aws:SourceVpce`\.

The following IAM policy statement requires the principal to access AWS only from the specified network range\. With this policy statement, all access must originate from that range\. This includes the case of someone who's using a presigned URL for S3 on Outposts\. To use this example, replace the *`user input placeholders`* with your own information\.

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

For an example bucket policy that uses the `aws:SourceIP` AWS global condition key to restrict access to an S3 on Outposts bucket to a specific network range, see [Setting up IAM with S3 on Outposts](S3OutpostsIAM.md)\.

## Who can create a presigned URL<a name="S3Outpostswho-presigned-url"></a>

Anyone with valid security credentials can create a presigned URL\. But for a user in the VPC to successfully access an object, the presigned URL must be created by someone who has permission to perform the operation that the presigned URL is based upon\.

You can use the following credentials to create a presigned URL:
+ **IAM instance profile** – Valid up to 6 hours\.
+ **AWS Security Token Service** – Valid up to 36 hours when signed with permanent credentials, such as the credentials of the AWS account root user or an IAM user\.
+ **IAM user** – Valid up to 7 days when you're using AWS Signature Version 4\.

  To create a presigned URL that's valid for up to 7 days, first delegate IAM user credentials \(the access key and secret key\) to the SDK that you're using\. Then, generate a presigned URL by using AWS Signature Version 4\.

**Note**  
If you created a presigned URL by using a temporary token, the URL expires when the token expires, even if you created the URL with a later expiration time\.
Because presigned URLs grant access to your S3 on Outposts buckets to whoever has the URL, we recommend that you protect them appropriately\. For more information about protecting presigned URLs, see [Limiting presigned URL capabilities](#S3OutpostsPresignedUrlUploadObjectLimitCapabilities)\.

## When does S3 on Outposts check the expiration date and time of a presigned URL?<a name="S3Outpostspresigned-url-when-checked"></a>

At the time of the HTTP request, S3 on Outposts checks the expiration date and time of a signed URL\. For example, if a client begins to download a large file immediately before the expiration time, the download continues even if the expiration time passes during the download\. However, if the connection drops and the client tries to restart the download after the expiration time passes, the download fails\.

For more information about using a presigned URL to share or upload objects, see the following topics\.

**Topics**
+ [Limiting presigned URL capabilities](#S3OutpostsPresignedUrlUploadObjectLimitCapabilities)
+ [Who can create a presigned URL](#S3Outpostswho-presigned-url)
+ [When does S3 on Outposts check the expiration date and time of a presigned URL?](#S3Outpostspresigned-url-when-checked)
+ [Sharing objects by using presigned URLs](S3OutpostsShareObjectPresignedURL.md)
+ [Generating a presigned URL to upload an object to an S3 on Outposts bucket](S3OutpostsPresignedUrlUploadObject.md)