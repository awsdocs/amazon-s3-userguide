# Downloading objects in Requester Pays buckets<a name="ObjectsinRequesterPaysBuckets"></a>

Because requesters are charged for downloading data from Requester Pays buckets, the requests must contain a special parameter, `x-amz-request-payer`, which confirms that the requester knows that they will be charged for the download\. To access objects in Requester Pays buckets, requests must include one of the following\.
+ For DELETE, GET, HEAD, POST, and PUT requests, include `x-amz-request-payer : requester` in the header
+ For signed URLs, include `x-amz-request-payer=requester` in the request

If the request succeeds and the requester is charged, the response includes the header `x-amz-request-charged:requester`\. If `x-amz-request-payer` is not in the request, Amazon S3 returns a 403 error and charges the bucket owner for the request\.

**Note**  
Bucket owners do not need to add `x-amz-request-payer` to their requests\.  
Ensure that you have included `x-amz-request-payer` and its value in your signature calculation\. For more information, see [Constructing the CanonicalizedAmzHeaders Element](RESTAuthentication.md#RESTAuthenticationConstructingCanonicalizedAmzHeaders)\.

## Using the REST API<a name="get-requester-pays-rest"></a>

**To download objects from a Requester Pays bucket**
+  Use a `GET` request to download an object from a Requester Pays bucket, as shown in the following request\.

  ```
  1. GET / [destinationObject] HTTP/1.1
  2. Host: [BucketName].s3.amazonaws.com
  3. x-amz-request-payer : requester
  4. Date: Wed, 01 Mar 2009 12:00:00 GMT
  5. Authorization: AWS [Signature]
  ```

If the GET request succeeds and the requester is charged, the response includes `x-amz-request-charged:requester`\.

Amazon S3 can return an `Access Denied` error for requests that try to get objects from a Requester Pays bucket\. For more information, see [Error Responses](https://docs.aws.amazon.com/AmazonS3/latest/API/ErrorResponses.html) in the *Amazon Simple Storage Service API Reference*\.

## Using the AWS CLI<a name="get-requester-pays-cli"></a>

To download objects from a Requester Pays bucket using the AWS CLI, you specify `--request-payer requester` as part of your `get-object` request\. For more information, see [get\-object](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/get-object.html) in the *AWS CLI Reference*\.