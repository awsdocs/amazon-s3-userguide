# Working with Range and partNumber headers<a name="range-get-olap"></a>

When working with large objects in Amazon S3 Object Lambda, you can use the `Range` HTTP header to download a specified byte range from an object\. To fetch different byte ranges from within the same object, you can use concurrent connections to Amazon S3\. You can also specify the `partNumber` parameter \(an integer between 1 and 10,000\), which performs a ranged request for the specified part of the object\.

Because there are multiple ways that you might want to handle a request that includes the `Range` or `partNumber` parameters, S3 Object Lambda doesn't apply these parameters to the transformed object\. Instead, your AWS Lambda function must implement this functionality as needed for your application\.

To use the `Range` and `partNumber` parameters with S3 Object Lambda, you do the following: 
+ Enable these parameters in your Object Lambda access point configuration\.
+ Write a Lambda function that can handle requests that include these parameters\.

The following steps describe how to accomplish this\.

## Step 1: Configure your Object Lambda access point<a name="range-get-olap-step-1"></a>

By default, Object Lambda access points respond with an HTTP status code 501 \(Not Implemented\) error to any `GetObject` or `HeadObject` request that contains a `Range` or `partNumber` parameter, either in the headers or query parameters\. 

To enable an Object Lambda access point to accept such requests, you must update your Object Lambda access point configuration by using the AWS Management Console, AWS Command Line Interface \(AWS CLI\), or AWS SDKs\. For more information about updating your Object Lambda access point configuration, see [Creating Object Lambda access points](olap-create.md)\. 

## Step 2: Implement `Range` or `partNumber` handling in your Lambda function<a name="range-get-olap-step-2"></a>

When your Object Lambda access point invokes your Lambda function with a ranged `GetObject` or `HeadObject` request, the `Range` or `partNumber` parameter is included in the event context\. The location of the parameter in the event context depends on which parameter was used and how it was included in the original request to the Object Lambda access point, as explained in the following table\. 


| Parameter | Event context location | 
| --- | --- | 
|  `Range` \(header\)  |  `userRequest.headers.Range`  | 
|  `Range` \(query parameter\)  |  `userRequest.url` \(query parameter `Range`\)  | 
|  `partNumber`  |  `userRequest.url` \(query parameter `partNumber`\)  | 

**Important**  
The provided presigned URL for your Object Lambda access point doesn't contain the `Range` or `partNumber` parameter from the original request\. If you want to retrieve only the specified range from Amazon S3, you must add the necessary parameter to the presigned URL\.

After you extract the `Range` or `partNumber` value, you can take one of the following approaches, based on your application's needs:

1. **Map the requested `Range` or `partNumber` to the transformed object \(recommended\)\.** 

   The most reliable way to handle `Range` or `partNumber` requests is to do the following: 
   + Retrieve the full object from Amazon S3\.
   + Transform the object\.
   + Apply the requested `Range` or `partNumber` parameters to the transformed object\.

   To do this, use the provided presigned URL to fetch the entire object from Amazon S3 and then process the object as needed\. For an example Lambda function that processes a `Range` parameter in this way, see [ this sample](https://github.com/aws-samples/amazon-s3-object-lambda-default-configuration/blob/main/function/nodejs_14_x/src/response/range_mapper.ts) in the AWS Samples GitHub repository\.

1. **Map the requested `Range` or `partNumber` to the presigned URL\.**

   In some cases, your Lambda function can map the requested `Range` or `partNumber` directly to the presigned URL to retrieve only part of the object from Amazon S3\. This approach is appropriate only if your transformation meets both of the following criteria:

   1. Your transformation function can be applied to partial object ranges\.

   1. The `Range` or `partNumber` parameter acts on the same data in the original object as in the transformed object\.

   For example, a transformation function that converts all characters in an ASCII\-encoded object to uppercase meets both of the preceding criteria\. The transform can be applied to part of an object, and applying the `Range` or `partNumber` parameter before the transformation achieves the same result as applying the parameter after the transformation\.

   By contrast, a function that reverses the characters in an ASCII\-encoded object doesn't meet these criteria\. Such a function meets criterion 1, because it can be applied to partial object ranges\. However, it doesn't meet criterion 2, because applying the `Range` parameter before the transformation achieves different results than applying the parameter after the transformation\. 

   For example, consider a request to apply the function to the first three characters of an object with the contents `abcdefg`\. Applying the `Range` parameter before the transformation retrieves only `abc` and then reverses the data, returning `cba`\. But if the parameter is applied after the transformation, the function retrieves the entire object, reverses it, and then applies the `Range` parameter, returning `gfe`\. Because these results are different, this function should not apply the `Range` parameter when retrieving the object from Amazon S3\. Instead, it should retrieve the entire object, perform the transformation, and only then apply the `Range` parameter\. 
**Warning**  
In many cases, applying the `Range` or `partNumber` parameter to the presigned URL will result in unexpected behavior by the Lambda function or the requesting client\. Unless you are sure that your application will work properly when retrieving only a partial object from Amazon S3, we recommend that you retrieve and transform full objects as described earlier in approach A\. 

   If your application meets the criteria described earlier in approach B, you can simplify your AWS Lambda function and minimize data\-transfer costs by fetching only the requested object range and then running your transformation on that range\. 

   The following Java code example demonstrates how to do the following: 
   + Retrieve the `Range` header from the `GetObject` request\.
   + Add the `Range` header to the presigned URL that Lambda can use to retrieve the requested range from Amazon S3\.

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