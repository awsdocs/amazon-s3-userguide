# Writing Lambda functions for S3 Object Lambda access points<a name="olap-writing-lambda"></a>

This section details how to write AWS Lambda functions for use with Amazon S3 Object Lambda access points\.

**Topics**
+ [Working with `GetObject` requests in Lambda](#olap-getobject-response)
+ [Working with `HeadObject` requests in Lambda](#olap-headobject)
+ [Working with `ListObjects` requests in Lambda](#olap-listobjects)
+ [Working with `ListObjectsV2` requests in Lambda](#olap-listobjectsv2)
+ [Event context format and usage](olap-event-context.md)
+ [Working with Range and partNumber headers](range-get-olap.md)

## Working with `GetObject` requests in Lambda<a name="olap-getobject-response"></a>

This section assumes that your Object Lambda access point is configured to call the Lambda function for `GetObject`\. S3 Object Lambda includes the Amazon S3 API operation, `WriteGetObjectResponse`, which enables the Lambda function to provide customized data and response headers to the `GetObject` caller\. 

`WriteGetObjectResponse` gives you extensive control over the status code, response headers, and response body, based on your processing needs\. You can use `WriteGetObjectResponse` to respond with the whole transformed object, portions of the transformed object, or other responses based on the context of your application\. The following section shows unique examples of using the `WriteGetObjectResponse` API operation\.
+ **Example 1:** Respond with HTTP status code 403 \(Forbidden\) 
+ **Example 2:** Respond with a transformed image
+ **Example 3:** Stream compressed content

**Example 1: Respond with HTTP status code 403 \(Forbidden\) **

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

**Example 2: Respond with a transformed image**

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
        // of the WriteGetObjectResponse request.
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
    In this case, we're resizing .png images that are stored in S3 and are accessible through the presigned URL
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

    // Retrieve the operation context object from the event. This object indicates where the WriteGetObjectResponse request
    // should be delivered and has a presigned URL in 'inputS3Url' where we can download the requested object from.
    const { getObjectContext } = event;
    const { outputRoute, outputToken, inputS3Url } = getObjectContext;

    // In this case, we're resizing .png images that are stored in S3 and are accessible through the presigned URL
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

**Example 3: Stream compressed content**

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
Although S3 Object Lambda allows up to 60 seconds to send a complete response to the caller through the `WriteGetObjectResponse` request, the actual amount of time available might be less\. For example, your Lambda function timeout might be less than 60 seconds\. In other cases, the caller might have more stringent timeouts\. 

For the original caller to receive a response other than HTTP status code 500 \(Internal Server Error\), the `WriteGetObjectResponse` call must be completed\. If the Lambda function returns, with an exception or otherwise, before the `WriteGetObjectResponse` API operation is called, the original caller receives a 500 \(Internal Server Error\) response\. Exceptions thrown during the time it takes to complete the response result in truncated responses to the caller\. If the Lambda function receives an HTTP status code 200 \(OK\) response from the `WriteGetObjectResponse` API call, then the original caller has sent the complete request\. The Lambda function's response, whether an exception is thrown or not, is ignored by S3 Object Lambda\.

When calling the `WriteGetObjectResponse` API operation, Amazon S3 requires the route and request token from the event context\. For more information, see [Event context format and usage](olap-event-context.md)\.

The route and request token parameters are required to connect the `WriteGetObjectResult` response with the original caller\. Even though it is always appropriate to retry 500 \(Internal Server Error\) responses, because the request token is a single\-use token, subsequent attempts to use it might result in HTTP status code 400 \(Bad Request\) responses\. Although the call to `WriteGetObjectResponse` with the route and request tokens doesn't need to be made from the invoked Lambda function, it must be made by an identity in the same account\. The call also must be completed before the Lambda function finishes execution\.

## Working with `HeadObject` requests in Lambda<a name="olap-headobject"></a>

This section assumes that your Object Lambda access point is configured to call the Lambda function for `HeadObject`\. Lambda will receive a JSON payload that contains a key called `headObjectContext`\. Inside the context, there is a single property called `inputS3Url`, which is a presigned URL for the supporting access point for `HeadObject`\.

The presigned URL will include the following properties if they're specified: 
+ `versionId` \(in the query parameters\)
+ `requestPayer` \(in the `x-amz-request-payer` header\)
+ `expectedBucketOwner` \(in the `x-amz-expected-bucket-owner` header\)

Other properties won't be presigned, and thus won't be included\. Non\-signed options sent as headers can be added manually to the request when calling the presigned URL that's found in the `userRequest` headers\. Server\-side encryption options are not supported for `HeadObject`\.

For the request syntax URI parameters, see [https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadObject.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_HeadObject.html) in the *Amazon Simple Storage Service API Reference*\.

The following example shows a Lambda JSON input payload for `HeadObject`\.

```
{
  "xAmzRequestId": "requestId",
  "**headObjectContext**": {
    "**inputS3Url**": "https://my-s3-ap-111122223333.s3-accesspoint.us-east-1.amazonaws.com/example?X-Amz-Security-Token=<snip>"
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

Your Lambda function should return a JSON object that contains the headers and values that will be returned for the `HeadObject` call\.

The following example shows the structure of the Lambda response JSON for `HeadObject`\.

```
{
    "statusCode": <number>; // Required
    "errorCode": <string>;
    "errorMessage": <string>;
    "headers": {
        "Accept-Ranges": <string>,
        "x-amz-archive-status": <string>,
        "x-amz-server-side-encryption-bucket-key-enabled": <boolean>,
        "Cache-Control": <string>,
        "Content-Disposition": <string>,
        "Content-Encoding": <string>,
        "Content-Language": <string>,
        "Content-Length": <number>, // Required
        "Content-Type": <string>,
        "x-amz-delete-marker": <boolean>,
        "ETag": <string>,
        "Expires": <string>,
        "x-amz-expiration": <string>,
        "Last-Modified": <string>,
        "x-amz-missing-meta": <number>,
        "x-amz-object-lock-mode": <string>,
        "x-amz-object-lock-legal-hold": <string>,
        "x-amz-object-lock-retain-until-date": <string>,
        "x-amz-mp-parts-count": <number>,
        "x-amz-replication-status": <string>,
        "x-amz-request-charged": <string>,
        "x-amz-restore": <string>,
        "x-amz-server-side-encryption": <string>,
        "x-amz-server-side-encryption-customer-algorithm": <string>,
        "x-amz-server-side-encryption-aws-kms-key-id": <string>,
        "x-amz-server-side-encryption-customer-key-MD5": <string>,
        "x-amz-storage-class": <string>,
        "x-amz-tagging-count": <number>,
        "x-amz-version-id": <string>,
        <x-amz-meta-headers>: <string>, // user-defined metadata 
        "x-amz-meta-meta1": <string>, // example of the user-defined metadata header, it will need the x-amz-meta prefix
        "x-amz-meta-meta2": <string>
        ...
    };
}
```

The following example shows how to use the presigned URL to populate your response by modifying the header values as needed before returning the JSON\.

------
#### [ Python ]



```
import requests

def lambda_handler(event, context):
    print(event)
    
    # Extract the presigned URL from the input.
    s3_url = event["headObjectContext"]["inputS3Url"]

    # Get the head of the object from S3.     
    response = requests.head(s3_url)
    
    # Return the error to S3 Object Lambda (if applicable).           
    if (response.status_code >= 400):
        return {
            "statusCode": response.status_code,
            "errorCode": "RequestFailure",                         
            "errorMessage": "Request to S3 failed"    
    }
    
    # Store the headers in a dictionary.
    response_headers = dict(response.headers)

    # This obscures Content-Type in a transformation, it is optional to add
    response_headers["Content-Type"] = "" 

    # Return the headers to S3 Object Lambda.     
    return {
        "statusCode": response.status_code,
        "headers": response_headers     
        }
```

------

## Working with `ListObjects` requests in Lambda<a name="olap-listobjects"></a>

This section assumes that your Object Lambda access point is configured to call the Lambda function for `ListObjects`\. Lambda will receive the JSON payload with a new object named `listObjectsContext`\. `listObjectsContext`contains a single property, `inputS3Url`, which is a presigned URL for the supporting access point for `ListObjects`\.

Unlike `GetObject` and `HeadObject`, the presigned URL will include the following properties if they're specified:
+ All the query parameters
+ `requestPayer` \(in the `x-amz-request-payer` header\) 
+ `expectedBucketOwner` \(in the `x-amz-expected-bucket-owner` header\)

For the request syntax URI parameters, see [https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListObjects.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListObjects.html) in the *Amazon Simple Storage Service API Reference*\.

**Important**  
We recommend that you use the newer version, [ListObjectsV2](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListObjectsV2.html), when developing applications\. For backward compatibility, Amazon S3 continues to support `ListObjects`\.

The following example shows the Lambda JSON input payload for `ListObjects`\.

```
{
    "xAmzRequestId": "requestId",
     "**listObjectsContext**": {
     "**inputS3Url**": "https://my-s3-ap-111122223333.s3-accesspoint.us-east-1.amazonaws.com/?X-Amz-Security-Token=<snip>",
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

Your Lambda function should return a JSON object that contains the status code, list XML result, or error information that will be returned from S3 Object Lambda\.

The following example demonstrates how to use the presigned URL to call Amazon S3 and use the result to populate a response, including error checking\.

------
#### [ Python ]

```
import requests import xmltodict

def lambda_handler(event, context):
    
    # Extract the presigned URL from the input.
    s3_url = event["listObjectsContext"]["inputS3Url"]

    # Get the head of the object from Amazon S3.     
    response = requests.get(s3_url)

    # Return the error to S3 Object Lambda (if applicable).     
    if (response.status_code >= 400):
        error = xmltodict.parse(response.content)         
        return {
            "statusCode": response.status_code,
            "errorCode": error["Error"]["Code"],
            "errorMessage": error["Error"]["Message"]
        }

    # Store the XML result in a dict.
    response_dict = xmltodict.parse(response.content)
    
    # This obscures StorageClass in a transformation, it is optional to add   
    for item in response_dict['ListBucketResult']['Contents']:
        item['StorageClass'] = ""

    # Convert back to XML.
    listResultXml = xmltodict.unparse(response_dict)

    # Return the list to S3 Object Lambda.     
    return {
        'statusCode': 200,
        'listResultXml': listResultXml
    }
```

------

The following example shows the structure of the Lambda response JSON for `ListObjects`\.

```
{ 
  "statusCode": <number>; // Required
  "errorCode": <string>;
  "errorMessage": <string>;
  "listResultXml": <string>;
}
```

## Working with `ListObjectsV2` requests in Lambda<a name="olap-listobjectsv2"></a>

This section assumes that your Object Lambda access point is configured to call the Lambda function for `ListObjectsV2`\. Lambda will receive the JSON payload with a new object named `listObjectsV2Context`\. `listObjectsV2Context` contains a single property, `inputS3Url`, which is a presigned URL for the supporting access point for `ListObjectsV2`\.

Unlike `GetObject` and `HeadObject`, the presigned URL will include the following properties, if they're specified: 
+ All the query parameters
+ `requestPayer` \(in the `x-amz-request-payer` header\) 
+ `expectedBucketOwner` \(in the `x-amz-expected-bucket-owner` header\)

For the request syntax URI parameters, see [https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListObjectsV2.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_ListObjectsV2.html) in the *Amazon Simple Storage Service API Reference*\.

The following example shows the Lambda JSON input payload for `ListObjectsV2`\.

```
{
    "xAmzRequestId": "requestId",
     "**listObjectsV2Context**": {
     "**inputS3Url**": "https://my-s3-ap-111122223333.s3-accesspoint.us-east-1.amazonaws.com/?list-type=2&X-Amz-Security-Token=<snip>",
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

Your Lambda function should return a JSON object that contains the status code, list XML result, or error information that will be returned from S3 Object Lambda\.

The following example demonstrates how to use the presigned URL to call Amazon S3 and use the result to populate a response, including error checking\.

------
#### [ Python ]

```
import requests import xmltodict

def lambda_handler(event, context):
    
    # Extract the presigned URL from the input.
    s3_url = event["listObjectsV2Context"]["inputS3Url"]

    # Get the head of the object from Amazon S3.     
    response = requests.get(s3_url)

    # Return the error to S3 Object Lambda (if applicable).     
    if (response.status_code >= 400):
        error = xmltodict.parse(response.content)         
        return {
            "statusCode": response.status_code,
            "errorCode": error["Error"]["Code"],
            "errorMessage": error["Error"]["Message"]
        }

    # Store the XML result in a dict.
    response_dict = xmltodict.parse(response.content)

    # This obscures StorageClass in a transformation, it is optional to add   
    for item in response_dict['ListBucketResult']['Contents']:
        item['StorageClass'] = ""

    # Convert back to XML.
    listResultXml = xmltodict.unparse(response_dict)

    # Return the list to S3 Object Lambda.     
    return {
        'statusCode': 200,
        'listResultXml': listResultXml
    }
```

------

The following example shows the structure of the Lambda response JSON for `ListObjectsV2`\.

```
{ 
  "statusCode": <number>; // Required
  "errorCode": <string>;
  "errorMessage": <string>;
  "listResultXml": <string>;
}
```