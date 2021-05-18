# Writing and debugging Lambda functions for S3 Object Lambda Access Points<a name="olap-writing-lambda"></a>

This section details about writing and debugging Lambda functions for use with Object Lambda access points\.

**Topics**
+ [Working with WriteGetObjectResponse](#olap-getobject-response)
+ [Debugging S3 Object Lambda](#olap-debugging-lambda)
+ [Working with Range and partNumber headers](#range-get-olap)
+ [Event context format and usage](#olap-event-context)

## Working with WriteGetObjectResponse<a name="olap-getobject-response"></a>

S3 Object Lambda exposes a new Amazon S3 API, WriteGetObjectResponse which enables the Lambda function to provide customized data and response headers to the GetObject caller\. WriteGetObjectResponse affords the Lambda author extensive control over the status code, response headers and response body based on their processing needs\. You can use WriteGetObjectResponse to respond with the whole transformed object, portions of the transformed object, or other responses based on the context of your application\. The following section shows unique examples of using the WriteGetObjectResponse\.
+ **Example 1:** Respond with a 403 Forbidden 
+ **Example 2:** Respond with a transformed image
+ **Example 3:** Stream compressed content

**Example 1:**

You can use WriteGetObjectResponse to respond with a 403 Forbidden based on the content of the object\.

------
#### [ Java ]



```
package com.amazon.s3.objectlambda;

import com.amazonaws.services.lambda.runtime.Context;
import com.amazonaws.services.lambda.runtime.events.S3ObjectLambdaEvent;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3Client;
import com.amazonaws.services.s3.model.WriteGetObjectResponseRequest;

import java.io.ByteArrayInputStream;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class Example1 {

    public void handleRequest(S3ObjectLambdaEvent event, Context context) throws Exception {
        AmazonS3 s3Client = AmazonS3Client.builder().build();

        // We're checking to see if the request contains all of the information we need.
        // If it does not, we send a 4XX response and a custom error code and message.
        // If we're happy with the request, we retrieve the object from S3 and stream it
        // to the client unchanged.
        var tokenIsNotPresent = !event.getUserRequest().getHeaders().containsKey("requiredToken");
        if (tokenIsNotPresent) {
            s3Client.writeGetObjectResponse(new WriteGetObjectResponseRequest()
                    .withRequestRoute(event.outputRoute())
                    .withRequestToken(event.outputToken())
                    .withStatusCode(403)
                    .withContentLength(0L).withInputStream(new ByteArrayInputStream(new byte[0]))
                    .withErrorCode("MissingRequiredToken")
                    .withErrorMessage("The required token was not present in the request."));
            return;
        }

        // Prepare the presigned URL for use and make the request to S3.
        HttpClient httpClient = HttpClient.newBuilder().build();
        var presignedResponse = httpClient.send(
                HttpRequest.newBuilder(new URI(event.inputS3Url())).GET().build(),
                HttpResponse.BodyHandlers.ofInputStream());

        // Stream the original bytes back to the caller.
        s3Client.writeGetObjectResponse(new WriteGetObjectResponseRequest()
                .withRequestRoute(event.outputRoute())
                .withRequestToken(event.outputToken())
                .withInputStream(presignedResponse.body()));
    }
}
```

------
#### [ Python ]



```
import boto3
import requests 

def handler(event, context):
    s3 = boto3.client('s3')

    """
    Retrieve the operation context object from event. This has info to where the WriteGetObjectResponse request
    should be delivered and a presigned URL in `inputS3Url` where we can download the requested object from.
    The `userRequest` object has information related to the user which made this `GetObject` request to S3OL.
    """
    get_context = event["getObjectContext"]
    user_request_headers = event["userRequest"]["headers"]

    route = get_context["outputRoute"]
    token = get_context["outputToken"]
    s3_url = get_context["inputS3Url"]

    # Check for the presence of a `CustomHeader` header and deny or allow based on that header
    is_token_present = "SuperSecretToken" in user_request_headers

    if is_token_present:
        # If the user presented our custom `SuperSecretToken` header we send the requested object back to the user.
        response = requests.get(s3_url)
        s3.write_get_object_response(RequestRoute=route, RequestToken=token, Body=response.content)
    else:
        # If the token is not present we send an error back to the user. 
        s3.write_get_object_response(RequestRoute=route, RequestToken=token, StatusCode=403,
        ErrorCode="NoSuperSecretTokenFound", ErrorMessage="The request was not secret enough.")

    # Gracefully exit the Lambda function
    return { 'status_code': 200 }
```

------
#### [ NodeJS ]



```
const { S3 } = require('aws-sdk');
const axios = require('axios').default;

exports.handler = async (event) => {
    const s3 = new S3();

    // Retrieve the operation context object from event. This has info to where the WriteGetObjectResponse request
    // should be delivered and a presigned URL in `inputS3Url` where we can download the requested object from.
    // The `userRequest` object has information related to the user which made this `GetObject` request to S3OL.
    const { userRequest, getObjectContext } = event;
    const { outputRoute, outputToken, inputS3Url } = getObjectContext;

    // Check for the presence of a `CustomHeader` header and deny or allow based on that header
    const isTokenPresent = Object
        .keys(userRequest.headers)
        .includes("SuperSecretToken");

    if (!isTokenPresent) {
        // If the token is not present we send an error back to the user. Notice the `await` infront of the request as
        // we want to wait for this request to finish sending before moving on. 
        await s3.writeGetObjectResponse({
            RequestRoute: outputRoute,
            RequestToken: outputToken,
            StatusCode: 403,
            ErrorCode: "NoSuperSecretTokenFound",
            ErrorMessage: "The request was not secret enough.",
        }).promise();
    } else {
        // If the user presented our custom `SuperSecretToken` header we send the requested object back to the user.
        // Again notice the presence of `await`.
        const presignedResponse = await axios.get(inputS3Url);
        await s3.writeGetObjectResponse({
            RequestRoute: outputRoute,
            RequestToken: outputToken,
            Body: presignedResponse.data,
        }).promise();
    }

    // Gracefully exit the Lambda function
    return { statusCode: 200 };
}
```

------

**Example 2:**

When performing an image transformation, you may find that you need all the bytes of the source object before you can start processing them\. Consequently, your WriteGetObjectResponse will return the whole object to the requesting application in one go\.

------
#### [ Java ]



```
package com.amazon.s3.objectlambda;

import com.amazonaws.services.lambda.runtime.Context;
import com.amazonaws.services.lambda.runtime.events.S3ObjectLambdaEvent;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3Client;
import com.amazonaws.services.s3.model.WriteGetObjectResponseRequest;

import javax.imageio.ImageIO;
import java.awt.image.BufferedImage;
import java.awt.Image;
import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class Example2 {

    private static final int HEIGHT = 250;
    private static final int WIDTH = 250;

    public void handleRequest(S3ObjectLambdaEvent event, Context context) throws Exception {
        AmazonS3 s3Client = AmazonS3Client.builder().build();
        HttpClient httpClient = HttpClient.newBuilder().build();

        // Prepare the presigned URL for use and make the request to S3.
        var presignedResponse = httpClient.send(
                HttpRequest.newBuilder(new URI(event.inputS3Url())).GET().build(),
                HttpResponse.BodyHandlers.ofInputStream());

        // The entire image is loaded into memory here so that we can resize it.
        // Once the resizing is completed, we write the bytes into the body
        // of the WriteGetObjectResponse.
        var originalImage = ImageIO.read(presignedResponse.body());
        var resizingImage = originalImage.getScaledInstance(WIDTH, HEIGHT, Image.SCALE_DEFAULT);
        var resizedImage = new BufferedImage(WIDTH, HEIGHT, BufferedImage.TYPE_INT_RGB);
        resizedImage.createGraphics().drawImage(resizingImage, 0, 0, WIDTH, HEIGHT, null);

        var baos = new ByteArrayOutputStream();
        ImageIO.write(resizedImage, "png", baos);

        // Stream the bytes back to the caller.
        s3Client.writeGetObjectResponse(new WriteGetObjectResponseRequest()
                .withRequestRoute(event.outputRoute())
                .withRequestToken(event.outputToken())
                .withInputStream(new ByteArrayInputStream(baos.toByteArray())));
    }
}
```

------
#### [ Python ]



```
import boto3
import requests 
import io
from PIL import Image

def handler(event, context):
    """
    Retrieve the operation context object from event. This has info to where the WriteGetObjectResponse request
    should be delivered and a presigned URL in `inputS3Url` where we can download the requested object from.
    The `userRequest` object has information related to the user which made this `GetObject` request to S3OL.
    """
    get_context = event["getObjectContext"]
    route = get_context["outputRoute"]
    token = get_context["outputToken"]
    s3_url = get_context["inputS3Url"]

    """
    In this case we're resizing `.png` images which are stored in S3 and are accessible via the presigned url
    `inputS3Url`.
    """
    image_request = requests.get(s3_url)
    image = Image.open(io.BytesIO(image_request.content))
    image.thumbnail((256,256), Image.ANTIALIAS)

    transformed = io.BytesIO()
    image.save(transformed, "png")

    # Sending the resized image back to the client
    s3 = boto3.client('s3')
    s3.write_get_object_response(Body=transformed.getvalue(), RequestRoute=route, RequestToken=token)

    # Gracefully exit the Lambda function
    return { 'status_code': 200 }
```

------
#### [ NodeJS ]



```
const { S3 } = require('aws-sdk');
const axios = require('axios').default;
const sharp = require('sharp');

exports.handler = async (event) => {
    const s3 = new S3();

    // Retrieve the operation context object from event. This has info to where the WriteGetObjectResponse request
    // should be delivered and a presigned URL in `inputS3Url` where we can download the requested object from
    const { getObjectContext } = event;
    const { outputRoute, outputToken, inputS3Url } = getObjectContext;

    // In this case we're resizing `.png` images which are stored in S3 and are accessible via the presigned url
    // `inputS3Url`.
    const { data } = await axios.get(inputS3Url, { responseType: 'arraybuffer' });

    // Resizing the image
    const resized = await sharp(data)
        .resize({ width: 256, height: 256 })
        .toBuffer();

    // Sending the resized image back to the client
    await s3.writeGetObjectResponse({
        RequestRoute: outputRoute,
        RequestToken: outputToken,
        Body: resized,
    }).promise();

    // Gracefully exit the Lambda function
    return { statusCode: 200 };
}
```

------

**Example 3:**

When compressing objects, compressed data is produced incrementally\. Consequently, your WriteGetObjectResponse can be used to return the compressed data as soon as it's ready\. As shown in this example, it is not necessary to know the length of the completed transformation\.

------
#### [ Java ]



```
package com.amazon.s3.objectlambda;

import com.amazonaws.services.lambda.runtime.events.S3ObjectLambdaEvent;
import com.amazonaws.services.lambda.runtime.Context;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3Client;
import com.amazonaws.services.s3.model.WriteGetObjectResponseRequest;

import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;

public class Example3 {

    public void handleRequest(S3ObjectLambdaEvent event, Context context) throws Exception {
        AmazonS3 s3Client = AmazonS3Client.builder().build();
        HttpClient httpClient = HttpClient.newBuilder().build();

        // Request the original object from S3.
        var presignedResponse = httpClient.send(
                HttpRequest.newBuilder(new URI(event.inputS3Url())).GET().build(),
                HttpResponse.BodyHandlers.ofInputStream());

        // We're consuming the incoming response body from the presigned request,
        // applying our transformation on that data and emitting the transformed bytes
        // into the body of the WriteGetObjectResponse request as soon as they're ready.
        // This example compresses the data from S3, but any processing pertinent
        // to your application can be performed here.
        var bodyStream = new GZIPCompressingInputStream(presignedResponse.body());

        // Stream the bytes back to the caller.
        s3Client.writeGetObjectResponse(new WriteGetObjectResponseRequest()
                .withRequestRoute(event.outputRoute())
                .withRequestToken(event.outputToken())
                .withInputStream(bodyStream));
    }

}
```

------
#### [ Python ]



```
import boto3
import requests
import zlib
from botocore.config import Config


"""
A helper class to work with content iterators. Takes an interator and compresses the bytes that come from it. It
implements `read` and `__iter__` so the SDK can stream the response 
"""
class Compress:
    def __init__(self, content_iter):
        self.content = content_iter
        self.compressed_obj = zlib.compressobj()

    def read(self, _size):
        for data in self.__iter__()
            return data

    def __iter__(self):
        while True:
            data = next(self.content)
            chunk = self.compressed_obj.compress(data)
            if not chunk:
                break

            yield chunk

        yield self.compressed_obj.flush()


def handler(event, context):
    """
    Setting the `payload_signing_enabled` property to False will allow us to send a streamed response back to the client
    in this scenario a streamed response means that the bytes are not buffered into memory as we're compressing them
    but are sent straight to the user
    """
    my_config = Config(
        region_name='eu-west-1',
        signature_version='s3v4',
        s3={
            "payload_signing_enabled": False
        }
    )
    s3 = boto3.client('s3', config=my_config)

    """
    Retrieve the operation context object from event. This has info to where the WriteGetObjectResponse request
    should be delivered and a presigned URL in `inputS3Url` where we can download the requested object from.
    The `userRequest` object has information related to the user which made this `GetObject` request to S3OL.
    """
    get_context = event["getObjectContext"]
    route = get_context["outputRoute"]
    token = get_context["outputToken"]
    s3_url = get_context["inputS3Url"]

    # Compress the `get` request stream
    with requests.get(s3_url, stream=True) as r:
        compressed = Compress(r.iter_content())

        # Send the stream back to the client
        s3.write_get_object_response(Body=compressed, RequestRoute=route, RequestToken=token, ContentType="text/plain",
                                     ContentEncoding="gzip")

    # Gracefully exit the Lambda function
    return {'status_code': 200}
```

------
#### [ NodeJS ]



```
const { S3 } = require('aws-sdk');
const axios = require('axios').default;
const zlib = require('zlib');

exports.handler = async (event) => {
    const s3 = new S3();

    // Retrieve the operation context object from event. This has info to where the WriteGetObjectResponse request
    // should be delivered and a presigned URL in `inputS3Url` where we can download the requested object from
    const { getObjectContext } = event;
    const { outputRoute, outputToken, inputS3Url } = getObjectContext;

    // Let's download the object from S3 and process it as a stream as it might be a huge object and we don't want to
    // buffer it in memory. Notice the `await` as we want to wait for `writeGetObjectResponse` to complete before we can
    // exit the Lambda function 
    await axios({
        method: 'GET',
        url: inputS3Url,
        responseType: 'stream',
    }).then(
        // Gzip the stream
        response => response.data.pipe(zlib.createGzip())
    ).then(
        // Finally send the gzip-ed stream back to the client
        stream => s3.writeGetObjectResponse({
            RequestRoute: outputRoute,
            RequestToken: outputToken,
            Body: stream,
            ContentType: "text/plain",
            ContentEncoding: "gzip",
        }).promise()
    );

    // Gracefully exit the Lambda function
    return { statusCode: 200 };
}
```

------

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