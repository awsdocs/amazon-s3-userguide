# Configuring Requester Pays on a bucket<a name="RequesterPaysExamples"></a>

You can configure an Amazon S3 bucket to be a *Requester Pays* bucket so that the requester pays the cost of the request and data download instead of the bucket owner\.

This section provides examples of how to configure Requester Pays on an Amazon S3 bucket using the console and the REST API\.

## Using the S3 console<a name="configure-requester-pays-console"></a>

**To enable Requester Pays for an S3 bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want to enable Requester Pays for\.

1. Choose **Properties**\.

1. Under **Requester pays**, choose **Edit**\.

1. Choose **Enable**, and choose **Save changes**\.

   Amazon S3 enables Requester Pays for your bucket and displays your **Bucket overview**\. Under **Requester pays**, you see **Enabled**\.

## Using the REST API<a name="RequesterPaysBucketConfiguration"></a>

Only the bucket owner can set the `RequestPaymentConfiguration.payer` configuration value of a bucket to `BucketOwner` \(the default\) or `Requester`\. Setting the `requestPayment` resource is optional\. By default, the bucket is not a Requester Pays bucket\.

To revert a Requester Pays bucket to a regular bucket, you use the value `BucketOwner`\. Typically, you would use `BucketOwner` when uploading data to the Amazon S3 bucket, and then you would set the value to `Requester` before publishing the objects in the bucket\.

**To set requestPayment**
+ Use a `PUT` request to set the `Payer` value to `Requester` on a specified bucket\.

  ```
  1. PUT ?requestPayment HTTP/1.1
  2. Host: [BucketName].s3.amazonaws.com
  3. Content-Length: 173
  4. Date: Wed, 01 Mar 2009 12:00:00 GMT
  5. Authorization: AWS [Signature]
  6. 
  7. <RequestPaymentConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
  8. <Payer>Requester</Payer>
  9. </RequestPaymentConfiguration>
  ```

If the request succeeds, Amazon S3 returns a response similar to the following\.

```
1. HTTP/1.1 200 OK
2. x-amz-id-2: [id]
3. x-amz-request-id: [request_id]
4. Date: Wed, 01 Mar 2009 12:00:00 GMT
5. Content-Length: 0
6. Connection: close
7. Server: AmazonS3
8. x-amz-request-charged:requester
```

You can set Requester Pays only at the bucket level\. You can't set Requester Pays for specific objects within the bucket\.

You can configure a bucket to be `BucketOwner` or `Requester` at any time\. However, there might be a few minutes before the new configuration value takes effect\.

**Note**  
Bucket owners who give out presigned URLs should consider carefully before configuring a bucket to be Requester Pays, especially if the URL has a long lifetime\. The bucket owner is charged each time the requester uses a presigned URL that uses the bucket owner's credentials\. 