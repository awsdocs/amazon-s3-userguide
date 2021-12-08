# Using an AWS CloudFormation template to automate S3 Object Lambda setup<a name="olap-using-cfn-template"></a>

You can use an AWS CloudFormation template to quickly create an Object Lambda access point\. The AWS CloudFormation template automatically creates relevant resources, configures IAM roles, and sets up a Lambda function that automatically handles requests through the Object Lambda access point\. With the AWS CloudFormation template, you can implement best practices, improve your security posture, and reduce errors caused by manual processes\.

This [GitHub repository](https://github.com/aws-samples/amazon-s3-object-lambda-default-configuration) contains the AWS CloudFormation template and AWS Lambda function source code\. For instructions on how to use the template, see [Creating Object Lambda Access Points](https://docs.aws.amazon.com/AmazonS3/latest/userguide/olap-create.html)\.

The Lambda function provided in the template does not run any transformation, and returns your objects as\-is from your Amazon S3 bucket\. You can clone the function and add your own transformation code to modify and process data as it is returned to an application\. 

## Modifying the template<a name="modifying-cfn-template"></a>

**Creating a new Supporting Access Point**  
You can create a new supporting Access Point by passing the following parameter as part of the `aws cloudformation deploy` command to create when deploying the template\.

```
CreateNewSupportingAccessPoint=true
```

**Configuring a function payload**  
You can configure a payload to provide supplemental data to the Lambda function by passing the following parameter as part of the `aws cloudformation deploy` command when deploying the template\.

```
LambdaFunctionPayload="format=csv"
```

**Enabling Amazon CloudWatch monitoring**  
You can enable CloudWatch monitoring by passing the following parameter as part of the `aws cloudformation deploy` command when deploying the template\.

```
EnableCloudWatchMonitoring=true
```

This will enable your Object Lambda access point for Amazon S3 request metrics and create two CloudWatch alarms to monitor client\-side and server\-side errors\.

**Note**  
Amazon CloudWatch usage will incur additional costs\. For more information on Amazon S3 request metrics, see [Monitoring and logging access points](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-points-monitoring-logging.html)

**Configuring provisioned concurrency**  
To optimize latency, you can configure provisioned concurrency for the Lambda function backing the Object Lambda access point by editing the template to include the following lines under `Resources`\.

```
LambdaFunctionVersion:
      Type: AWS::Lambda::Version
      Properties:
        FunctionName: !Ref LambdaFunction
        ProvisionedConcurrencyConfig:
            ProvisionedConcurrentExecutions: Integer
```

**Note**  
You will incur additional charges for provisioning concurrency\. For more information, see [Managing Lambda provisioned concurrency](https://docs.aws.amazon.com/lambda/latest/dg/provisioned-concurrency.html) in the *AWS Lambda Developer Guide*\.

## Modifying the Lambda function<a name="modifying-lambda-function"></a>

**Changing header values**  
By default, the Lambda function forwards all headers, except Content\-Length and ETag, from the pre\-signed URL request to the `GetObject` client\. Based on your transformation code in the Lambda function, you can choose to send new header values to the `GetObject` client\.

You can update your Lambda function to send new header values by passing them in the `WriteGetObjectResponse` API\.

For example, if your Lambda function translates text in Amazon S3 objects to a different language, you can pass a new value in the `Content-Language` header\. You can do this by modifying the `writeResponse` function as below\.

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
You can update your Lambda function to send new header values by passing them in the [https://docs.aws.amazon.com/AmazonS3/latest/API/API_WriteGetObjectResponse.html#API_WriteGetObjectResponse_RequestSyntax](https://docs.aws.amazon.com/AmazonS3/latest/API/API_WriteGetObjectResponse.html#API_WriteGetObjectResponse_RequestSyntax) API\.

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
You can return a custom status code to the `GetObject` client by passing it in the [https://docs.aws.amazon.com/AmazonS3/latest/API/API_WriteGetObjectResponse.html#API_WriteGetObjectResponse_RequestSyntax](https://docs.aws.amazon.com/AmazonS3/latest/API/API_WriteGetObjectResponse.html#API_WriteGetObjectResponse_RequestSyntax) API\.

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

**Applying Range and partNumber to the source object**  
By default, the Object Lambda access point created by the AWS CloudFormation template can handle range and part number parameters\. The Lambda function applies the range or part number requested to the transformed object\. To do so, it needs to download the whole object and run the transformation\. In some cases, your transformed object ranges might map exactly to your source object ranges\. This means that requesting byte range A\-B on your source object and running the transformation may produce the same result as requesting for the whole object, running the transformation, and returning byte range A\-B on the transformed object\.

In such cases, you can change the Lambda function implementation to apply the range or part number directly to the source object\. This improves the overall function latency and memory required\. For more information, see [Working with Range and partNumber headers](https://docs.aws.amazon.com/AmazonS3/latest/userguide/olap-writing-lambda.html#range-get-olap)\.

**Disabling Range and partNumber handling**  
By default, the Object Lambda access point created by the AWS CloudFormation template can handle range and part number parameters\. If you do not need this, you can disable it by removing the following lines from the template\.

```
AllowedFeatures:
  - GetObject-Range
  - GetObject-PartNumber
```

**Transforming large objects**  
By default, the Lambda function processes the entire object in memory before it can start streaming the response to S3 Object Lambda\. You can modify the function to stream the response as it performs the transformation\. This helps reduce the transformation latency and the Lambda function memory size\. For an example implementation, see the [Stream compressed content example](https://docs.aws.amazon.com/AmazonS3/latest/userguide/olap-writing-lambda.html#olap-getobject-response)\.