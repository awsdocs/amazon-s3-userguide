# Automate S3 Object Lambda setup with a CloudFormation template<a name="olap-using-cfn-template"></a>

You can use an AWS CloudFormation template to quickly create an Amazon S3 Object Lambda Access Point\. The CloudFormation template automatically creates relevant resources, configures AWS Identity and Access Management \(IAM\) roles, and sets up an AWS Lambda function that automatically handles requests through the Object Lambda Access Point\. With the CloudFormation template, you can implement best practices, improve your security posture, and reduce errors caused by manual processes\.

This [GitHub repository](https://github.com/aws-samples/amazon-s3-object-lambda-default-configuration) contains the CloudFormation template and Lambda function source code\. For instructions on how to use the template, see [Creating Object Lambda Access Points](olap-create.md)\.

The Lambda function provided in the template does not run any transformation\. Instead, it returns your objects as\-is from your S3 bucket\. You can clone the function and add your own transformation code to modify and process data as it is returned to an application\. For more information about modifying your function, see [Modifying the Lambda function](#modifying-lambda-function) and [Writing Lambda functions for S3 Object Lambda Access Points](olap-writing-lambda.md)\. 

## Modifying the template<a name="modifying-cfn-template"></a>

**Creating a new supporting access point**  
S3 Object Lambda uses two access points, an Object Lambda Access Point and a standard S3 access point, which is referred to as the *supporting access point*\. When you make a request to an Object Lambda Access Point, S3 either invokes Lambda on your behalf, or it delegates the request to the supporting access point, depending upon the S3 Object Lambda configuration\. You can create a new supporting access point by passing the following parameter as part of the `aws cloudformation deploy` command when deploying the template\.

```
CreateNewSupportingAccessPoint=true
```

**Configuring a function payload**  
You can configure a payload to provide supplemental data to the Lambda function by passing the following parameter as part of the `aws cloudformation deploy` command when deploying the template\.

```
LambdaFunctionPayload="format=json"
```

**Enabling Amazon CloudWatch monitoring**  
You can enable CloudWatch monitoring by passing the following parameter as part of the `aws cloudformation deploy` command when deploying the template\.

```
EnableCloudWatchMonitoring=true
```

This parameter enables your Object Lambda Access Point for Amazon S3 request metrics and creates two CloudWatch alarms to monitor client\-side and server\-side errors\.

**Note**  
Amazon CloudWatch usage will incur additional costs\. For more information about Amazon S3 request metrics, see [Monitoring and logging access points](access-points-monitoring-logging.md)\.  
For pricing details, see [CloudWatch pricing](http://aws.amazon.com/cloudwatch/pricing/)\. 

**Configuring provisioned concurrency**  
To reduce latency, you can configure provisioned concurrency for the Lambda function that's backing the Object Lambda Access Point by editing the template to include the following lines under `Resources`\.

```
LambdaFunctionVersion:
      Type: AWS::Lambda::Version
      Properties:
        FunctionName: !Ref LambdaFunction
        ProvisionedConcurrencyConfig:
            ProvisionedConcurrentExecutions: Integer
```

**Note**  
You will incur additional charges for provisioning concurrency\. For more information about provisioned concurrency, see [Managing Lambda provisioned concurrency](https://docs.aws.amazon.com/lambda/latest/dg/provisioned-concurrency.html) in the *AWS Lambda Developer Guide*\.  
For pricing details, see [AWS Lambda pricing](https://aws.amazon.com/lambda/pricing/)\.

## Modifying the Lambda function<a name="modifying-lambda-function"></a>

**Changing header values for a `GetObject` request**  
By default, the Lambda function forwards all headers, except `Content-Length` and `ETag`, from the presigned URL request to the `GetObject` client\. Based on your transformation code in the Lambda function, you can choose to send new header values to the `GetObject` client\.

You can update your Lambda function to send new header values by passing them in the `WriteGetObjectResponse` API operation\.

For example, if your Lambda function translates text in Amazon S3 objects to a different language, you can pass a new value in the `Content-Language` header\. You can do this by modifying the `writeResponse` function as follows:

```
async function writeResponse (s3Client: S3, requestContext: GetObjectContext, transformedObject: Buffer,
  headers: Headers): Promise<PromiseResult<{}, AWSError>> {
  const { algorithm, digest } = getChecksum(transformedObject);

  return s3Client.writeGetObjectResponse({
    RequestRoute: requestContext.outputRoute,
    RequestToken: requestContext.outputToken,
    Body: transformedObject,
    Metadata: {
      'body-checksum-algorithm': algorithm,
      'body-checksum-digest': digest
    },
    ...headers,
    ContentLanguage: 'my-new-language'
  }).promise();
}
```

For a full list of supported headers, see [https://docs.aws.amazon.com/AmazonS3/latest/API/API_WriteGetObjectResponse.html#API_WriteGetObjectResponse_RequestSyntax](https://docs.aws.amazon.com/AmazonS3/latest/API/API_WriteGetObjectResponse.html#API_WriteGetObjectResponse_RequestSyntax) in the *Amazon Simple Storage Service API Reference*\.

**Returning metadata headers**  
You can update your Lambda function to send new header values by passing them in the [https://docs.aws.amazon.com/AmazonS3/latest/API/API_WriteGetObjectResponse.html#API_WriteGetObjectResponse_RequestSyntax](https://docs.aws.amazon.com/AmazonS3/latest/API/API_WriteGetObjectResponse.html#API_WriteGetObjectResponse_RequestSyntax) API operation request\.

```
async function writeResponse (s3Client: S3, requestContext: GetObjectContext, transformedObject: Buffer,
  headers: Headers): Promise<PromiseResult<{}, AWSError>> {
  const { algorithm, digest } = getChecksum(transformedObject);

  return s3Client.writeGetObjectResponse({
    RequestRoute: requestContext.outputRoute,
    RequestToken: requestContext.outputToken,
    Body: transformedObject,
    Metadata: {
      'body-checksum-algorithm': algorithm,
      'body-checksum-digest': digest,
      'my-new-header': 'my-new-value' 
    },
    ...headers
  }).promise();
}
```

**Returning a new status code**  
You can return a custom status code to the `GetObject` client by passing it in the [https://docs.aws.amazon.com/AmazonS3/latest/API/API_WriteGetObjectResponse.html#API_WriteGetObjectResponse_RequestSyntax](https://docs.aws.amazon.com/AmazonS3/latest/API/API_WriteGetObjectResponse.html#API_WriteGetObjectResponse_RequestSyntax) API operation request\.

```
async function writeResponse (s3Client: S3, requestContext: GetObjectContext, transformedObject: Buffer,
  headers: Headers): Promise<PromiseResult<{}, AWSError>> {
  const { algorithm, digest } = getChecksum(transformedObject);

  return s3Client.writeGetObjectResponse({
    RequestRoute: requestContext.outputRoute,
    RequestToken: requestContext.outputToken,
    Body: transformedObject,
    Metadata: {
      'body-checksum-algorithm': algorithm,
      'body-checksum-digest': digest
    },
    ...headers,
    StatusCode: Integer
  }).promise();
}
```

For a full list of supported status codes, see [https://docs.aws.amazon.com/AmazonS3/latest/API/API_WriteGetObjectResponse.html#API_WriteGetObjectResponse_RequestSyntax](https://docs.aws.amazon.com/AmazonS3/latest/API/API_WriteGetObjectResponse.html#API_WriteGetObjectResponse_RequestSyntax) in the *Amazon Simple Storage Service API Reference*\.

**Applying `Range` and `partNumber` parameters to the source object**  
By default, the Object Lambda Access Point created by the CloudFormation template can handle the `Range` and `partNumber` parameters\. The Lambda function applies the range or part number requested to the transformed object\. To do so, the function must download the whole object and run the transformation\. In some cases, your transformed object ranges might map exactly to your source object ranges\. This means that requesting byte range A\-B on your source object and running the transformation might produce the same result as requesting the whole object, running the transformation, and returning byte range A\-B on the transformed object\.

In such cases, you can change the Lambda function implementation to apply the range or part number directly to the source object\. This approach reduces the overall function latency and memory required\. For more information, see [Working with Range and partNumber headers](range-get-olap.md)\.

**Disabling `Range` and `partNumber` handling**  
By default, the Object Lambda Access Point created by the CloudFormation template can handle the `Range` and `partNumber` parameters\. If you don't need this behavior, you can disable it by removing the following lines from the template:

```
AllowedFeatures:
  - GetObject-Range
  - GetObject-PartNumber
  - HeadObject-Range 
  - HeadObject-PartNumber
```

**Transforming large objects**  
By default, the Lambda function processes the entire object in memory before it can start streaming the response to S3 Object Lambda\. You can modify the function to stream the response as it performs the transformation\. Doing so helps reduce the transformation latency and the Lambda function memory size\. For an example implementation, see the [Stream compressed content example](olap-writing-lambda.md#olap-getobject-response)\.