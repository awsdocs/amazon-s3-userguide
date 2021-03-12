--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Deleting an object from an MFA delete\-enabled bucket<a name="UsingMFADelete"></a>

If a bucket's versioning configuration is MFA delete enabled, the bucket owner must include the `x-amz-mfa` request header in requests to permanently delete an object version or change the versioning state of the bucket\. Requests that include `x-amz-mfa` must use HTTPS\. 

The header's value is the concatenation of your authentication device's serial number, a space, and the authentication code displayed on it\. If you don't include this request header, the request fails\.

For more information about authentication devices, see [Multi\-factor Authentication](http://aws.amazon.com/iam/details/mfa/)\.

**Example — Deleting an object from an MFA delete\-enabled bucket**  
The following example deletes `my-image.jpg` \(with the specified version\), which is in a bucket configured with MFA delete enabled\.   
Note the space between *\[SerialNumber\]* and *\[AuthenticationCode\]*\. For more information, see [DeleteObject](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectDELETE.html) in the *Amazon Simple Storage Service API Reference*\.  

```
1. DELETE /my-image.jpg?versionId=3HL4kqCxf3vjVBH40Nrjfkd HTTPS/1.1
2. Host: bucketName.s3.amazonaws.com
3. x-amz-mfa: 20899872 301749
4. Date: Wed, 28 Oct 2009 22:32:00 GMT
5. Authorization: AWS AKIAIOSFODNN7EXAMPLE:0RQf4/cRonhpaBX5sCYVf1bNRuU=
```

For more information about enabling MFA delete, see [Configuring MFA delete](MultiFactorAuthenticationDelete.md)\.