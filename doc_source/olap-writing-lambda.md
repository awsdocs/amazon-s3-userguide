# Writing and debugging Lambda functions for S3 Object Lambda Access Points<a name="olap-writing-lambda"></a>

This section details about writing and debugging Lambda functions for use with Object Lambda access points\.

**Topics**
+ [Working with WriteGetObjectResponse](#olap-getobject-response)
+ [Debugging S3 Object Lambda](#olap-debugging-lambda)
+ [Working with Range and partNumber headers](#range-get-olap)
+ [Event context format and usage](#olap-event-context)

## Working with WriteGetObjectResponse<a name="olap-getobject-response"></a>

S3 Object Lambda exposes a new Amazon S3 API, WriteGetObjectResponse which enables the Lambda function to provide customized data and response headers to the GetObject caller\. WriteGetObjectResponse affords the Lambda author extensive control over the status code, response headers and response body based on their processing needs\. You can use WriteGetObjectResponse to respond with the whole transformed object, portions of the transformed object, or other responses based on the context of your application\. The following section shows four unique examples of using the WriteGetObjectResponse to return customized objects\. In each example WriteGetObjectResponse will be called only once\.
+ **Example 1:** Respond with a 403 Forbidden 
+ **Example 2:** Respond with a transformed image
+ **Example 3:** Respond with a CSV with rows/columns masked in\-place
+ **Example 4:** Respond with a CSV with rows/columns filtered out 

**Example 1:**

You can use WriteGetObjectResponse to respond with a 403 Forbidden based on the content of the object\.

```
private void respondWithCustomError(int statusCode, String errorCode, String errorMessage, ObjectLambdaEvent event) {
    var s3Client = getS3Client();
    s3Client.writeGetObjectResponse(
            request -> request
                    .requestRoute(event.getGetObjectContext().getOutputRoute())
                    .requestToken(event.getGetObjectContext().getOutputToken())

                    // Here we can explicitly set the Http Status Code, and the appropriate error code
                    // and error message for our application and caller.
                    .statusCode(statusCode)
                    .errorCode(errorCode)
                    .errorMessage(errorMessage),
            RequestBody.empty());
}

// And in usage:
if(requestContainsRequiredToken(user) == false) {
    respondWithCustomError(
            400, 
            "MissingToken", 
            "The request was missing the required token.", 
            event);
}
```

**Example 2:**

When performing an image transformation, you may find that you need all the bytes of the source object before you can start processing them\. Consequently, your WriteGetObjectResponse will return the whole object to the requesting application in one go\.

```
public void handleRequest(ObjectLambdaEvent event, Context context) throws Exception {
    // Prepare the presigned URL for use and make the request to S3.
    var presignedResponse = httpClient.send(
            prepareGetRequest(event),
            HttpResponse.BodyHandlers.ofByteArray());

    // Check the initial request from S3 for errors and propagate to the client as appropriate
    if(isError(presignedResponse.statusCode())) {
        respondWithError(event, presignedResponse);
        return;
    }

    // Pull the S3 response into memory and transform them.
    var s3ObjectData = presignedResponse.body();
    var transformedBytes = transformCompleteObject(s3ObjectData);

    // Write the response, fully formed, back to the caller.
    s3Client.writeGetObjectResponse(
            request -> request
                    .requestRoute(event.getGetObjectContext().getOutputRoute())
                    .requestToken(event.getGetObjectContext().getOutputToken()),
                    
                    // OPTIONALLY: Retrieve the original headers and metadata from 
                    // the presigned S3 request and propagate to the caller
                    .overrideConfiguration(req ->
                            presignedResponse.headers().map().forEach((k, v) ->
                                    req.putHeader("x-amz-fwd-header-" + k, Strings.join(v, ','))
                            )
                    )
            RequestBody.fromBytes(transformedBytes)
    );
}
```

**Example 3:**

When Masking data in\-place, you can perform the transformation on portions of the object\. Consequently, your WriteGetObjectResponse can be used to return the portions as their transformation is complete\. In this example, because the transformation involved in\-place masking, the size of the transformed object is the same as the object as it sits in S3\.

```
public void handleRequest(ObjectLambdaEvent event, Context context) throws Exception {
    // Prepare the presigned URL for use and make the request to S3.
    var presignedResponse = httpClient.send(
            prepareGetRequest(event),
            HttpResponse.BodyHandlers.ofInputStream());

    // Check the initial request from S3 for errors and propagate to the client as appropriate
    if(isError(presignedResponse.statusCode())) {
        respondWithError(event, presignedResponse);
        return;
    }

    // Extract the content-length header from the S3 response.
    int contentLength = extractContentLength(presignedResponse, context.getLogger());

    // Prepare to transform the bytes as they flow from the S3 Get response body
    // to the body of the WriteGetObjectResponse request.  The InputStreamTransform
    // needs only read what it needs before emitting transformed bytes.
    InputStream s3ObjectData = presignedResponse.body();
    InputStream transformedStream = new InputStreamTransform(s3ObjectData);

    // Write the response, providing our transforming stream, back to the caller.
    s3Client.writeGetObjectResponse(
            request -> request
                    .requestRoute(event.getGetObjectContext().getOutputRoute())
                    .requestToken(event.getGetObjectContext().getOutputToken()),
            RequestBody.fromInputStream(transformedStream, contentLength)
    );
}
```

**Example 4:**

When filtering rows/columns out of a CSV, you can perform the transformation on portions of the object\. Consequently, your WriteGetObjectResponse can be used to return the portions as their transformation is complete\. In this example, because the transformation involved filtering data out of the object, the resulting object size is unknown\. 

```
public void handleRequest(ObjectLambdaEvent event, Context context) throws Exception {
    // Prepare the presigned URL for use and make the request to S3.
    var presignedResponse = httpClient.send(
            prepareGetRequest(event),
            HttpResponse.BodyHandlers.ofInputStream());

    // Check the initial request from S3 for errors and propagate to the client as appropriate
    if(isError(presignedResponse.statusCode())) {
        respondWithError(event, presignedResponse);
        return;
    }

    // We're consuming the incoming response body from the presigned request,
    // applying our transformation on that data as it moves through the redaction process
    // and emitting the transformed bytes into the body of the WriteGetObjectResponse request.
    var redactor = new CsvRedactor(event);
    var flowable = Flowable
            .generate(
                    () -> new BufferedInputStream(presignedResponse.body()),
                    redactor::consumeTransformEmit,
                    BufferedInputStream::close);

    // Stream the bytes back to the caller.
    var wgorResponseFuture = s3AsyncClient.writeGetObjectResponse(
            request -> request
                    .requestRoute(event.getGetObjectContext().getOutputRoute())
                    .requestToken(event.getGetObjectContext().getOutputToken())
                    .overrideConfiguration(req -> {
                        presignedResponse.headers().map().forEach((k, v) -> {
                            req.putHeader(X_FWD_PRE + k, Strings.join(v, ','));
                        });
                    }),
            AsyncRequestBody.fromPublisher(flowable)
    );

    // Wait for the processing to complete and log the response of the 
    // call for debugging.
    logResponse(wgorResponseFuture.join(), context.getLogger());
}
```

**Note**  
While S3 Object Lambda allows up to 60 seconds to send a complete response to the caller via WriteGetObjectResponse the actual amount of time available may be less, for instance if your Lambda function timeout is less than 60 seconds\. In other cases the caller may have more stringent timeouts\. 

The WriteGetObjectResponse call must be made for the original caller to receive a non\-500 response\. If the Lambda function returns, exceptionally or otherwise, before the WriteGetObjectResponse API is called the original caller will receive a 500 response\. Exceptions thrown during the time it takes to complete the response will result in truncated responses to the caller\. If the Lambda receives a 200 Response from the WriteGetObjectResponse API call then the original caller has sent the complete request\. The Lambda response, exceptional or not, is ignored by S3 Object Lambda\.

When calling this API, S3 requires the route and request token from the event context\. For more information, see [Event context format and usage](#olap-event-context)\.

These parameters are required to connect the WriteGetObjectResult with the original caller\. While it is always appropriate to retry 500 responses, note that the request token is a single use token and subsequent attempts to use it may result in 400 Bad Request responses\. While the call to WriteGetObjectResponse with the route and request tokens does not need to be made from the invoked Lambda, it does need to be made by an identity in the same account and must be completed before the Lambda finishes execution\.

## Debugging S3 Object Lambda<a name="olap-debugging-lambda"></a>

Get Object requests to S3 Object Lambda access points may result in a new error responses when something goes wrong with the Lambda invocation or execution\. These errors follow the same format as the standard S3 errors\. For information about S3 Object Lambda errors, see [S3 Object Lambda Error Code List](https://docs.aws.amazon.com/AmazonS3/latest/API/ErrorResponses.html#S3ObjectLambdaErrorCodeList) in the *Amazon Simple Storage Service API Reference*\.

For more information on general Lambda function debugging see, [Monitoring and troubleshooting Lambda applications](https://docs.aws.amazon.com/lambda/latest/dg/lambda-monitoring.html ) in the *AWS Lambda Developer Guide*\.

For information about standard Amazon S3 errors, see [Error Responses](https://docs.aws.amazon.com/AmazonS3/latest/API/ErrorResponses.html) in the *Amazon Simple Storage Service API Reference*\.

You can enable request metrics in CloudWatch for your Object Lambda access points\. These metrics can be used to monitor the operational performance of your access point\.

CloudTrail Data Events can be enabled to get more granular logging about requests made to your Object Lambda access points\. For more information see, [Logging data events for trails](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-data-events-with-cloudtrail.html) in the *AWS CloudTrail User Guide*\.

## Working with Range and partNumber headers<a name="range-get-olap"></a>

When working with large objects you can use the Range HTTP header to download a specified byte\-range from an object fetching only the specified portion\. You can use concurrent connections to Amazon S3 to fetch different byte ranges from within the same object\. You can also use partNumber \(integer between 1 and 10,000\) which effectively performs a ‘ranged’ GET request for the specified part from the object\. For more information, see [GetObject Request Syntax](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html#API_GetObject_RequestSyntax) in the Amazon Simple Storage Service API Reference\.

When receiving a GET request, S3 Object Lambda invokes your specified Lambda function first, hence if your GET request contains range or part number parameters, you must ensure that your Lambda function is equipped to recognize and manage these parameters\. Because there can be multiple entities connected in such a setup \(requesting client and services like Lambda, S3, other\) it is advised that all involved entities interpret the requested range \(or partNumber\) in a uniform manner\. This ensures that the ranges the application is expecting match with the ranges your Lambda function is processing\. When building a function to handle requests with range headers test all combinations of response sizes, original object sizes, and request range sizes that your application plans to use\.

By default, S3 Object Lambda access points will respond with a 501 to any GetObject request that contains a range or part number parameter, either in the headers or query parameters\. You can confirm that your Lambda function is prepared to handle range or part requests by updating your Object Lambda access point configuration through the AWS Management Console or the AWS CLI\.

The following code example demonstrates how to retrieve the Range header from the GET request and add it to the presignedURL that Lambda can use to retrieve the requested range from S3\.

```
private HttpRequest.Builder applyRangeHeader(ObjectLambdaEvent event, HttpRequest.Builder presignedRequest) {
    var header = event.getUserRequest().getHeaders().entrySet().stream()
            .filter(e -> e.getKey().toLowerCase(Locale.ROOT).equals("range"))
            .findFirst();

    // Add check in the query string itself.
    header.ifPresent(entry -> presignedRequest.header(entry.getKey(), entry.getValue()));
    return presignedRequest;
}
```

Range requests to S3 can be made using headers or query parameters\. If the original request used the Range header it can be found in the event context at `userRequest.headers.Range`\. If the original request used a query parameter then it will be present in `userRequest.url` as ‘Range’\. In both cases, the presigned URL that is provided will not contain the specified range, and the range header should be added to it in order to retrieve the requested range from S3\.

Part requests to S3 are made using query parameters\. If the original request included a part number it can be found in the query parameters in `userRequest.url` as ‘partNumber’\. The presigned URL that is provided will not contain the specified partNumber\. 

## Event context format and usage<a name="olap-event-context"></a>

S3 Object Lambda provides context about the request being made in the event passed to Lambda\. The following shows an example request and field descriptions\. 

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
+ **`xAmzRequestId`** ‐ The Amazon S3 request ID for this request\. We recommend that you log this value to help with debugging\.
+ **`getObjectContext`** ‐ The input and output details for connections to Amazon S3 and S3 Object Lambda\.
  + **`inputS3Url`** ‐ A presigned URL that can be used to fetch the original object from Amazon S3\. The URL is signed using the original caller’s identity, and their permissions will apply when the URL is used\. If there are signed headers in the URL, the Lambda function must include these in the call to Amazon S3, except for the `Host`\.
  + **`outputRoute`** ‐ A routing token that is added to the S3 Object Lambda URL when the Lambda function calls `WriteGetObjectResponse`\.
  + **`outputToken`** ‐ An opaque token used by S3 Object Lambda to match the `WriteGetObjectResponse` call with the original caller\.
+ **`configuration`** ‐ Configuration information about the S3 Object Lambda access point\.
  + **`accessPointArn`** ‐ The Amazon Resource Name \(ARN\) of the S3 Object Lambda access point that received this request\.
  + **`supportingAccessPointArn`** ‐ The ARN of the supporting access point that is specified in the S3 Object Lambda access point configuration\.
  + **`payload`** ‐ Custom data that is applied to the S3 Object Lambda access point configuration\. S3 Object Lambda treats this as an opaque string, so it might need to be decoded before use\.
+ **`userRequest`** ‐ Information about the original call to S3 Object Lambda\.
  + **`url`** ‐ The decoded URL of the request as received by S3 Object Lambda, excluding any authorization\-related query parameters\.
  + **`headers`** ‐ A map of string to strings containing the HTTP headers and their values from the original call, excluding any authorization\-related headers\. If the same header appears multiple times, their values are combined into a comma\-delimited list\. The case of the original headers is retained in this map\.
+ **`userIdentity`** ‐ Details about the identity that made the call to S3 Object Lambda\. For more information see, [Logging data events for trails](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-user-identity.html) in the *AWS CloudTrail User Guide*\.
  + **`type`** ‐ The type of identity\.
  + **`accountId`** ‐ The AWS account to which the identity belongs\.
  + **`userName`** ‐ The friendly name of the identity that made the call\.
  + **`principalId`** ‐ The unique identifier for the identity that made the call\.
  + **`arn`** ‐ The ARN of the principal that made the call\. The last section of the ARN contains the user or role that made the call\.
  + **`sessionContext`** ‐ If the request was made with temporary security credentials, this element provides information about the session that was created for those credentials\.
  + **`invokedBy`** ‐ The name of the AWS service that made the request, such as Amazon EC2 Auto Scaling or AWS Elastic Beanstalk\.
  + **`sessionIssuer`** ‐ If the request was made with temporary security credentials, this element provides information about how the credentials were obtained\.
+ **`protocolVersion`** ‐ The version ID of the context provided\. The format of this field is `{Major Version}.{Minor Version}`\. The minor version numbers are always two\-digit numbers\. Any removal or change to the semantics of a field will necessitate a major version bump and will require active opt\-in\. Amazon S3 can add new fields at any time, at which point you might experience a minor version bump\. Due to the nature of software rollouts, it is possible that you might see multiple minor versions in use at once\.