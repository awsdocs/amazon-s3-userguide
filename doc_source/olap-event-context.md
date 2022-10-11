# Event context format and usage<a name="olap-event-context"></a>

Amazon S3 Object Lambda provides context about the request that's being made in the event that's passed to your AWS Lambda function\. The following shows an example request\. Descriptions of the fields are included after the example\.

```
{
    "xAmzRequestId": "requestId",
    "getObjectContext": {
        "inputS3Url": "https://my-s3-ap-111122223333.s3-accesspoint.us-east-1.amazonaws.com/example?X-Amz-Security-Token=<snip>",
        "outputRoute": "io-use1-001",
        "outputToken": "OutputToken"
    },
    "configuration": {
        "accessPointArn": "arn:aws:s3-object-lambda:us-east-1:111122223333:accesspoint/example-object-lambda-ap",
        "supportingAccessPointArn": "arn:aws:s3:us-east-1:111122223333:accesspoint/example-ap",
        "payload": "{}"
    },
    "userRequest": {
        "url": "https://object-lambda-111122223333.s3-object-lambda.us-east-1.amazonaws.com/example",
        "headers": {
            "Host": "object-lambda-111122223333.s3-object-lambda.us-east-1.amazonaws.com",
            "Accept-Encoding": "identity",
            "X-Amz-Content-SHA256": "e3b0c44298fc1example"
        }
    },
    "userIdentity": {
        "type": "AssumedRole",
        "principalId": "principalId",
        "arn": "arn:aws:sts::111122223333:assumed-role/Admin/example",
        "accountId": "111122223333",
        "accessKeyId": "accessKeyId",
        "sessionContext": {
            "attributes": {
                "mfaAuthenticated": "false",
                "creationDate": "Wed Mar 10 23:41:52 UTC 2021"
            },
            "sessionIssuer": {
                "type": "Role",
                "principalId": "principalId",
                "arn": "arn:aws:iam::111122223333:role/Admin",
                "accountId": "111122223333",
                "userName": "Admin"
            }
        }
    },
    "protocolVersion": "1.00"
}
```

The following fields are included in the request:
+ `xAmzRequestId` – The Amazon S3 request ID for this request\. We recommend that you log this value to help with debugging\.
+ `getObjectContext` – The input and output details for connections to Amazon S3 and S3 Object Lambda\.
  + `inputS3Url` – A presigned URL that can be used to fetch the original object from Amazon S3\. The URL is signed by using the original caller's identity, and that user's permissions will apply when the URL is used\. If there are signed headers in the URL, the Lambda function must include these headers in the call to Amazon S3, except for the `Host` header\.
  + `outputRoute` – A routing token that is added to the S3 Object Lambda URL when the Lambda function calls `WriteGetObjectResponse`\.
  + `outputToken` – An opaque token that's used by S3 Object Lambda to match the `WriteGetObjectResponse` call with the original caller\.
+ `configuration` – Configuration information about the Object Lambda access point\.
  + `accessPointArn` – The Amazon Resource Name \(ARN\) of the Object Lambda access point that received this request\.
  + `supportingAccessPointArn` – The ARN of the supporting access point that is specified in the Object Lambda access point configuration\.
  + `payload` – Custom data that is applied to the Object Lambda access point configuration\. S3 Object Lambda treats this data as an opaque string, so it might need to be decoded before use\.
+ `userRequest` – Information about the original call to S3 Object Lambda\.
  + `url` – The decoded URL of the request as received by S3 Object Lambda, excluding any authorization\-related query parameters\.
  + `headers` – A map of string to strings containing the HTTP headers and their values from the original call, excluding any authorization\-related headers\. If the same header appears multiple times, the values from each instance of the same header are combined into a comma\-delimited list\. The case of the original headers is retained in this map\.
+ `userIdentity` – Details about the identity that made the call to S3 Object Lambda\. For more information, see [Logging data events for trails](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-data-events-with-cloudtrail.html) in the *AWS CloudTrail User Guide*\.
  + `type` – The type of identity\.
  + `accountId` – The AWS account to which the identity belongs\.
  + `userName` – The friendly name of the identity that made the call\.
  + `principalId` – The unique identifier for the identity that made the call\.
  + `arn` – The ARN of the principal who made the call\. The last section of the ARN contains the user or role that made the call\.
  + `sessionContext` – If the request was made with temporary security credentials, this element provides information about the session that was created for those credentials\.
  + `invokedBy` – The name of the AWS service that made the request, such as Amazon EC2 Auto Scaling or AWS Elastic Beanstalk\.
  + `sessionIssuer` – If the request was made with temporary security credentials, this element provides information about how the credentials were obtained\.
+ `protocolVersion` – The version ID of the context provided\. The format of this field is `{Major Version}.{Minor Version}`\. The minor version numbers are always two\-digit numbers\. Any removal or change to the semantics of a field necessitates a major version bump and requires active opt\-in\. Amazon S3 can add new fields at any time, at which point you might experience a minor version bump\. Because of the nature of software rollouts, you might see multiple minor versions in use at once\.