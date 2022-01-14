# Writing and debugging AWS Lambda functions for Amazon S3 Object Lambda Access Points<a name="olap-writing-lambda"></a>

This section details how to write and debug Lambda functions for use with S3 Object Lambda access points\.

**Topics**
+ [Working with WriteGetObjectResponse](#olap-getobject-response)
+ [Debugging S3 Object Lambda](#olap-debugging-lambda)
+ [Working with Range and partNumber headers](#range-get-olap)
+ [Event context format and usage](#olap-event-context)

## Working with WriteGetObjectResponse<a name="olap-getobject-response"></a>

S3 Object Lambda includes a new Amazon S3 API operation, `WriteGetObjectResponse`, which enables the Lambda function to provide customized data and response headers to the `GetObject` caller\. `WriteGetObjectResponse` affords the Lambda author extensive control over the status code, response headers, and response body, based on their processing needs\. You can use `WriteGetObjectResponse` to respond with the whole transformed object, portions of the transformed object, or other responses based on the context of your application\. The following section shows unique examples of using the `WriteGetObjectResponse` API operation\.
+ **Example 1:** Respond with an HTTP status code 403 \(Forbidden\) 
+ **Example 2:** Respond with a transformed image
+ **Example 3:** Stream compressed content

**Example 1:**

You can use `WriteGetObjectResponse` to respond with the HTTP status code 403 \(Forbidden\) based on the content of the object\.

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

        // Check to see if the request contains all of the necessary information.
        // If it does not, send a 4XX response and a custom error code and message.
        // Otherwise, retrieve the object from S3 and stream it
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
    Retrieve the operation context object from the event. This object indicates where the WriteGetObjectResponse request
    should be delivered and contains a presigned URL in 'inputS3Url' where we can download the requested object from.
    The 'userRequest' object has information related to the user who made this 'GetObject' request to 
    S3 Object Lambda.
    """
    get_context = event["getObjectContext"]
    user_request_headers = event["userRequest"]["headers"]

    route = get_context["outputRoute"]
    token = get_context["outputToken"]
    s3_url = get_context["inputS3Url"]

    # Check for the presence of a 'CustomHeader' header and deny or allow based on that header.
    is_token_present = "SuperSecretToken" in user_request_headers

    if is_token_present:
        # If the user presented our custom 'SuperSecretToken' header, we send the requested object back to the user.
        response = requests.get(s3_url)
        s3.write_get_object_response(RequestRoute=route, RequestToken=token, Body=response.content)
    else:
        # If the token is not present, we send an error back to the user. 
        s3.write_get_object_response(RequestRoute=route, RequestToken=token, StatusCode=403,
        ErrorCode="NoSuperSecretTokenFound", ErrorMessage="The request was not secret enough.")

    # Gracefully exit the Lambda function.
    return { 'status_code': 200 }
```

------
#### [ Node\.js ]



```
const { S3 } = require('aws-sdk');
const axios = require('axios').default;

exports.handler = async (event) => {
    const s3 = new S3();

    // Retrieve the operation context object from the event. This object indicates where the WriteGetObjectResponse request
    // should be delivered and contains a presigned URL in 'inputS3Url' where we can download the requested object from.
    // The 'userRequest' object has information related to the user who made this 'GetObject' request to S3 Object Lambda.
    const { userRequest, getObjectContext } = event;
    const { outputRoute, outputToken, inputS3Url } = getObjectContext;

    // Check for the presence of a 'CustomHeader' header and deny or allow based on that header.
    const isTokenPresent = Object
        .keys(userRequest.headers)
        .includes("SuperSecretToken");

    if (!isTokenPresent) {
        // If the token is not present, we send an error back to the user. The 'await' in front of the request
        // indicates that we want to wait for this request to finish sending before moving on. 
        await s3.writeGetObjectResponse({
            RequestRoute: outputRoute,
            RequestToken: outputToken,
            StatusCode: 403,
            ErrorCode: "NoSuperSecretTokenFound",
            ErrorMessage: "The request was not secret enough.",
        }).promise();
    } else {
        // If the user presented our custom 'SuperSecretToken' header, we send the requested object back to the user.
        // Again, note the presence of 'await'.
        const presignedResponse = await axios.get(inputS3Url);
        await s3.writeGetObjectResponse({
            RequestRoute: outputRoute,
            RequestToken: outputToken,
            Body: presignedResponse.data,
        }).promise();
    }

    // Gracefully exit the Lambda function.
    return { statusCode: 200 };
}
```

------

**Example 2:**

When performing an image transformation, you might find that you need all the bytes of the source object before you can start processing them\. In this case, your `WriteGetObjectResponse` request returns the whole object to the requesting application in one call\.

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
    Retrieve the operation context object from the event. This object indicates where the WriteGetObjectResponse request
    should be delivered and has a presigned URL in 'inputS3Url' where we can download the requested object from.
    The 'userRequest' object has information related to the user who made this 'GetObject' request to 
    S3 Object Lambda.
    """
    get_context = event["getObjectContext"]
    route = get_context["outputRoute"]
    token = get_context["outputToken"]
    s3_url = get_context["inputS3Url"]

    """
    In this case, we're resizing '.png' images that are stored in S3 and are accessible through the presigned URL
    'inputS3Url'.
    """
    image_request = requests.get(s3_url)
    image = Image.open(io.BytesIO(image_request.content))
    image.thumbnail((256,256), Image.ANTIALIAS)

    transformed = io.BytesIO()
    image.save(transformed, "png")

    # Send the resized image back to the client.
    s3 = boto3.client('s3')
    s3.write_get_object_response(Body=transformed.getvalue(), RequestRoute=route, RequestToken=token)

    # Gracefully exit the Lambda function.
    return { 'status_code': 200 }
```

------
#### [ Node\.js ]



```
const { S3 } = require('aws-sdk');
const axios = require('axios').default;
const sharp = require('sharp');

exports.handler = async (event) => {
    const s3 = new S3();

    // Retrieve the operation context object from event. This object indicates where the WriteGetObjectResponse request
    // should be delivered and has a presigned URL in 'inputS3Url' where we can download the requested object from.
    const { getObjectContext } = event;
    const { outputRoute, outputToken, inputS3Url } = getObjectContext;

    // In this case, we're resizing '.png' images that are stored in S3 and are accessible through the presigned URL
    // 'inputS3Url'.
    const { data } = await axios.get(inputS3Url, { responseType: 'arraybuffer' });

    // Resize the image.
    const resized = await sharp(data)
        .resize({ width: 256, height: 256 })
        .toBuffer();

    // Send the resized image back to the client.
    await s3.writeGetObjectResponse({
        RequestRoute: outputRoute,
        RequestToken: outputToken,
        Body: resized,
    }).promise();

    // Gracefully exit the Lambda function.
    return { statusCode: 200 };
}
```

------

**Example 3:**

When you're compressing objects, compressed data is produced incrementally\. Consequently, you can use your `WriteGetObjectResponse` request to return the compressed data as soon as it's ready\. As shown in this example, you don't need to know the length of the completed transformation\.

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

        // Consume the incoming response body from the presigned request,
        // apply our transformation on that data, and emit the transformed bytes
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
implements 'read' and '__iter__' so that the SDK can stream the response. 
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
    Setting the 'payload_signing_enabled' property to False allows us to send a streamed response back to the client.
    in this scenario, a streamed response means that the bytes are not buffered into memory as we're compressing them,
    but instead are sent straight to the user.
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
    Retrieve the operation context object from the event. This object indicates where the WriteGetObjectResponse request
    should be delivered and has a presigned URL in 'inputS3Url' where we can download the requested object from.
    The 'userRequest' object has information related to the user who made this 'GetObject' request to S3 Object Lambda.
    """
    get_context = event["getObjectContext"]
    route = get_context["outputRoute"]
    token = get_context["outputToken"]
    s3_url = get_context["inputS3Url"]

    # Compress the 'get' request stream.
    with requests.get(s3_url, stream=True) as r:
        compressed = Compress(r.iter_content())

        # Send the stream back to the client.
        s3.write_get_object_response(Body=compressed, RequestRoute=route, RequestToken=token, ContentType="text/plain",
                                     ContentEncoding="gzip")

    # Gracefully exit the Lambda function.
    return {'status_code': 200}
```

------
#### [ Node\.js ]



```
const { S3 } = require('aws-sdk');
const axios = require('axios').default;
const zlib = require('zlib');

exports.handler = async (event) => {
    const s3 = new S3();

    // Retrieve the operation context object from the event. This object indicates where the WriteGetObjectResponse request
    // should be delivered and has a presigned URL in 'inputS3Url' where we can download the requested object from.
    const { getObjectContext } = event;
    const { outputRoute, outputToken, inputS3Url } = getObjectContext;

    // Download the object from S3 and process it as a stream, because it might be a huge object and we don't want to
    // buffer it in memory. Note the use of 'await' because we want to wait for 'writeGetObjectResponse' to finish 
    // before we can exit the Lambda function. 
    await axios({
        method: 'GET',
        url: inputS3Url,
        responseType: 'stream',
    }).then(
        // Gzip the stream.
        response => response.data.pipe(zlib.createGzip())
    ).then(
        // Finally send the gzip-ed stream back to the client.
        stream => s3.writeGetObjectResponse({
            RequestRoute: outputRoute,
            RequestToken: outputToken,
            Body: stream,
            ContentType: "text/plain",
            ContentEncoding: "gzip",
        }).promise()
    );

    // Gracefully exit the Lambda function.
    return { statusCode: 200 };
}
```

------

**Note**  
Although S3 Object Lambda allows up to 60 seconds to send a complete response to the caller through the `WriteGetObjectResponse` request, the actual amount of time available might be less\. For instance, your Lambda function timeout might be less than 60 seconds\. In other cases, the caller might have more stringent timeouts\. 

For the original caller to receive a non\-500 \(Internal Server Error\) response, the `WriteGetObjectResponse` call must be completed\. If the Lambda function returns, with an exception or otherwise, before the `WriteGetObjectResponse` API operation is called, the original caller receives a 500 response\. Exceptions thrown during the time it takes to complete the response result in truncated responses to the caller\. If the Lambda function receives a 200 \(OK\) response from the `WriteGetObjectResponse` API call, then the original caller has sent the complete request\. The Lambda function's response, whether an exception is thrown or not, is ignored by S3 Object Lambda\.

When calling this API operation, Amazon S3 requires the route and request token from the event context\. For more information, see [Event context format and usage](#olap-event-context)\.

These parameters are required to connect the `WriteGetObjectResult` response with the original caller\. Even though it is always appropriate to retry 500 responses, note that because the request token is a single\-use token, subsequent attempts to use it might result in 400 \(Bad Request\) responses\. Although the call to `WriteGetObjectResponse` with the route and request tokens does not need to be made from the invoked Lambda function, it must be made by an identity in the same account\. The call also must be completed before the Lambda function finishes execution\.

## Debugging S3 Object Lambda<a name="olap-debugging-lambda"></a>

`GetObject` requests to S3 Object Lambda access points might result in a new error response when something goes wrong with the Lambda function invocation or execution\. These errors follow the same format as standard Amazon S3 errors\. For information about S3 Object Lambda errors, see [S3 Object Lambda Error Code List](https://docs.aws.amazon.com/AmazonS3/latest/API/ErrorResponses.html#S3ObjectLambdaErrorCodeList) in the *Amazon Simple Storage Service API Reference*\.

For more information about general Lambda function debugging, see [Monitoring and troubleshooting Lambda applications](https://docs.aws.amazon.com/lambda/latest/dg/lambda-monitoring.html ) in the *AWS Lambda Developer Guide*\.

For information about standard Amazon S3 errors, see [Error Responses](https://docs.aws.amazon.com/AmazonS3/latest/API/ErrorResponses.html) in the *Amazon Simple Storage Service API Reference*\.

You can enable request metrics in Amazon CloudWatch for your Object Lambda access points\. These metrics can be used to monitor the operational performance of your access point\.

To get more granular logging about requests made to your Object Lambda access points, you can enable AWS CloudTrail data events\. For more information, see [Logging data events for trails](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-data-events-with-cloudtrail.html) in the *AWS CloudTrail User Guide*\.

## Working with Range and partNumber headers<a name="range-get-olap"></a>

When working with large objects, you can use the `Range` HTTP header to download a specified byte range from an object\. You can use concurrent connections to Amazon S3 to fetch different byte ranges from within the same object\. You can also specify the `partNumber` parameter \(an integer between 1 and 10,000\), which effectively performs a "ranged" GET request for the specified part from the object\. For more information, see [GetObject Request Syntax](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html#API_GetObject_RequestSyntax) in the *Amazon Simple Storage Service API Reference*\.

Because there are multiple ways that you might want to handle a request that includes the `Range` or `partNumber` parameters, S3 Object Lambda doesn't apply these parameters to the transformed object\. Instead, your Lambda function must implement this functionality as needed for your application\.

To use the `Range` and `partNumber` parameters with S3 Object Lambda, you enable these parameters for your Object Lambda access point, then write a Lambda function that can handle requests that include these parameters\. The following steps describe how to accomplish this\.

### Step 1: Configure your Object Lambda access point<a name="range-get-olap-step-1"></a>

By default, Object Lambda access points respond with an HTTP 501 \(Not Implemented\) error to any `GetObject` request that contains a `Range` or `partNumber` parameter, either in the headers or query parameters\. To enable an Object Lambda access point to accept such requests, you must update your Object Lambda access point configuration by using the AWS Management Console, AWS Command Line Interface \(AWS CLI\), or AWS SDKs\. For more information about updating your Object Lambda access point configuration, see [Creating Object Lambda Access Points](olap-create.md)\. 

### Step 2: Implement Range or partNumber handling in your Lambda function<a name="range-get-olap-step-2"></a>

When your Object Lambda access point invokes your Lambda function with a ranged `GetObject` request, the `Range` or `partNumber` parameter is included in the event context\. The location of the parameter in the event context depends on which parameter was used and how it was included in the original request to the Object Lambda access point, as explained in the following table\. 


| Parameter | Event context location | 
| --- | --- | 
|  `Range` \(header\)  |  `userRequest.headers.Range`  | 
|  `Range` \(query parameter\)  |  `userRequest.url` \(query parameter `Range`\)  | 
|  `partNumber`  |  `userRequest.url` \(query parameter `partNumber`\)  | 

**Important**  
The presigned URL provided does not contain the `Range` or `partNumber` parameter from the original request\. If you want to retrieve only the specified range from Amazon S3, you must add the parameter to the presigned URL\.

After you extract the `Range` or `partNumber` value, you can take one of the following approaches based on your application's needs:

1. **Map the requested Range or partNumber to the transformed object** \(recommended\)

   The most reliable way to handle `Range` or `partNumber` requests is to retrieve the full object from S3, transform the object, and then apply the requested `Range` or `partNumber` parameters to the transformed object\. To do this, use the provided presigned URL to fetch the entire object from Amazon S3 and then process the object as needed\. For an example Lambda function that processes a `Range` parameter in this way, see [ this sample](https://github.com/aws-samples/amazon-s3-object-lambda-default-configuration/blob/main/function/nodejs_14_x/src/response/range_mapper.ts) in the AWS Samples GitHub repository\.

1. **Map the requested Range or partNumber to the presigned URL**

   In some cases, your Lambda function can map the requested Range or partNumber directly to the presigned URL to retrieve only part of the object from Amazon S3\. This approach is appropriate only if your transformation meets both of the following criteria:

   1. Your transformation function can be applied to partial object ranges\.

   1. The `Range` or `partNumber` parameter acts on the same data in the original object as in the transformed object\.

   For example, a transformation function that converts all characters in an ASCII\-encoded object to uppercase meets both of the preceding criteria\. The transform can be applied to part of an object, and applying the `Range` or `partNumber` parameter before the transformation achieves the same result as applying the parameter after the transformation\.

   By contrast, a function that reverses the characters in an ASCII\-encoded object doesn't meet these criteria\. Such a function meets criterion 1, because it can be applied to partial object ranges\. However, it doesn't meet criterion 2, because applying the `Range` parameter before the transformation achieves different results than applying the parameter after the transformation\. 

   For example, consider a request to apply the function to the first three characters of an object with the contents `abcdefg`\. Applying the `Range` parameter before the transformation retrieves only `abc` and then reverses the data, returning `cba`\. But if the parameter is applied after the transformation, the function retrieves the entire object, reverses it, and then applies the `Range` parameter, returning `gfe`\. Because these results are different, this function should not apply the `Range` parameter when retrieving the object from Amazon S3\. Instead, it should retrieve the entire object, perform the transformation, and only then apply the `Range` parameter\. 
**Warning**  
In many cases, applying the `Range` or `partNumber` parameter to the presigned URL will result in unexpected behavior by the Lambda function or the requesting client\. Unless you are sure that your application will work properly when retrieving only a partial object from Amazon S3, we recommend that you retrieve and transform full objects as described earlier in approach A\. 

   If your application meets the criteria described earlier, you can simplify your AWS Lambda function and minimize data\-transfer costs by fetching only the requested object range and then running your transformation on that range\.

   The following Java code example demonstrates how to retrieve the `Range` header from the `GetObject` request and add it to the presigned URL that Lambda can use to retrieve the requested range from Amazon S3\.

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

## Event context format and usage<a name="olap-event-context"></a>

S3 Object Lambda provides context about the request being made in the event passed to your Lambda function\. The following shows an example request and field descriptions\. 

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
+ `xAmzRequestId` – The Amazon S3 request ID for this request\. We recommend that you log this value to help with debugging\.
+ `getObjectContext` – The input and output details for connections to Amazon S3 and S3 Object Lambda\.
  + `inputS3Url` – A presigned URL that can be used to fetch the original object from Amazon S3\. The URL is signed using the original caller's identity, and their permissions will apply when the URL is used\. If there are signed headers in the URL, the Lambda function must include these in the call to Amazon S3, except for the `Host` header\.
  + `outputRoute` – A routing token that is added to the S3 Object Lambda URL when the Lambda function calls `WriteGetObjectResponse`\.
  + `outputToken` – An opaque token used by S3 Object Lambda to match the `WriteGetObjectResponse` call with the original caller\.
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