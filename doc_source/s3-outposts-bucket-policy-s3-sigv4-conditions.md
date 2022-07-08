# AWS Signature Version 4 \(SigV4\) authentication\-specific policy keys<a name="s3-outposts-bucket-policy-s3-sigv4-conditions"></a>

The following table shows the condition keys related to AWS Signature Version 4 \(SigV4\) authentication that you can use with Amazon S3 on Outposts\. In a bucket policy, you can add these conditions to enforce specific behavior when requests are authenticated by using Signature Version 4\. For example policies, see [Bucket policy examples that use Signature Version 4\-related condition keys](#s3-outposts-bucket-policy-sig-v4-condition-key-example)\. For more information about authenticating requests using Signature Version 4, see [Authenticating requests \(AWS Signature Version 4\)](https://docs.aws.amazon.com/AmazonS3/latest/API/sig-v4-authenticating-requests.html) in the *Amazon Simple Storage Service API Reference*


**Applicable keys for `s3-outposts:*` actions or any of the S3 on Outposts actions**  

| Applicable keys | Description | 
| --- | --- | 
|  `s3-outposts:authType`  |  S3 on Outposts supports various methods of authentication\. To restrict incoming requests to use a specific authentication method, you can use this optional condition key\. For example, you can use this condition key to allow only the HTTP `Authorization` header to be used in request authentication\.  Valid values:  `REST-HEADER`  `REST-QUERY-STRING`   | 
|  `s3-outposts:signatureAge`  |  The length of time, in milliseconds, that a signature is valid in an authenticated request\. This condition works only for presigned URLs\. In Signature Version 4, the signing key is valid for up to seven days\. Therefore, the signatures are also valid for up to seven days\. For more information, see [Introduction to signing requests](https://docs.aws.amazon.com/AmazonS3/latest/API/sig-v4-authenticating-requests.html#signing-request-intro) in the *Amazon Simple Storage Service API Reference*\. You can use this condition to further limit the signature age\.  Example value: `600000`  | 
|  `s3-outposts:x-amz-content-sha256`  |  You can use this condition key to disallow unsigned content in your bucket\.  When you use Signature Version 4, for requests that use the `Authorization` header, you add the `x-amz-content-sha256` header in the signature calculation and then set its value to the hash payload\.  You can use this condition key in your bucket policy to deny any uploads where the payloads are not signed\. For example: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-outposts-bucket-policy-s3-sigv4-conditions.html) Valid value: `UNSIGNED-PAYLOAD`  | 

## Bucket policy examples that use Signature Version 4\-related condition keys<a name="s3-outposts-bucket-policy-sig-v4-condition-key-example"></a>

To use the following examples, replace the *`user input placeholders`* with your own information\.

**Example : `s3-outposts:signatureAge`**  
The following bucket policy denies any S3 on Outposts presigned URL request on objects in `example-outpost-bucket` if the signature is more than 10 minutes old\.   

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

**Example : `s3-outposts:authType`**  
The following bucket policy allows only requests that use the `Authorization` header for request authentication\. Any presigned URL requests will be denied since presigned URLs use query parameters to provide request and authentication information\. For more information, see [Authentication methods](https://docs.aws.amazon.com/AmazonS3/latest/API/sig-v4-authenticating-requests.html) in the *Amazon Simple Storage Service API Reference*\.  

```
 1. {
 2.    "Version": "2012-10-17",
 3.    "Statement": [
 4.          {
 5.                "Sid": "Allow only requests that use the Authorization header for request authentication. Deny presigned URL requests.",
 6.                "Effect": "Deny",
 7.                "Principal": {"AWS":"111122223333"},
 8.                "Action": "s3-outposts:*",
 9.                "Resource": "arn:aws:s3-outposts:us-east-1:111122223333:outpost/op-01ac5d28a6a232904/bucket/example-outpost-bucket/object/*",
10.                "Condition": {
11.                      "StringNotEquals": {
12.                            "s3-outposts:authType": "REST-HEADER"
13.                      }
14.                }
15.          }
16.    ]
17. }
```

**Example : `s3-outposts:x-amz-content-sha256`**  
The following bucket policy denies any uploads with unsigned payloads, such as uploads that are using presigned URLs\. For more information, see [Authenticating requests ](https://docs.aws.amazon.com/AmazonS3/latest/API/sigv4-query-string-auth.html#query-string-auth-v4-signing) and [Authentication methods](https://docs.aws.amazon.com/AmazonS3/latest/API/sig-v4-authenticating-requests.html) in the *Amazon Simple Storage Service API Reference*\.  

```
{
   "Version": "2012-10-17",
   "Statement": [
         {
               "Sid": "Deny uploads with unsigned payloads.",
               "Effect": "Deny",
               "Principal": {"AWS":"111122223333"},
               "Action": "s3-outposts:*",
               "Resource": "arn:aws:s3-outposts:us-east-1:111122223333:outpost/op-01ac5d28a6a232904/bucket/example-outpost-bucket/object/*",
               "Condition": {
                     "StringEquals": {
                           "s3-outposts:x-amz-content-sha256": "UNSIGNED-PAYLOAD"
                     }
               }
         }
   ]
}
```