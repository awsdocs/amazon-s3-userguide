# Using AWS built Lambda functions<a name="olap-examples"></a>

AWS provides some prebuilt AWS Lambda functions that you can use with Amazon S3 Object Lambda to detect and redact personally identifiable information \(PII\) and decompress S3 objects\. These Lambda functions are available in the AWS Serverless Application Repository\. You can select these functions through the AWS Management Console when you create your Object Lambda access point\. 

For more information about how to deploy serverless applications from the AWS Serverless Application Repository, see [Deploying Applications](https://docs.aws.amazon.com/serverlessrepo/latest/devguide/serverlessrepo-consuming-applications.html) in the *AWS Serverless Application Repository Developer Guide*\.

**Note**  
The following examples can be used only with `GetObject` requests\.

## Example 1: PII access control<a name="olap-examples-1"></a>

This Lambda function uses Amazon Comprehend, a natural language processing \(NLP\) service that uses machine learning to find insights and relationships in text\. This function automatically detects personally identifiable information \(PII\), such as names, addresses, dates, credit card numbers, and social security numbers in documents in your Amazon S3 bucket\. If you have documents in your bucket that include PII, you can configure the PII Access Control function to detect these PII entity types and restrict access to unauthorized users\.

To get started, deploy the following Lambda function in your account and add the Amazon Resource Name \(ARN\) for the function to your Object Lambda access point configuration\.

The following is an example ARN for this function:

```
arn:aws:serverlessrepo:us-east-1:111122223333:applications/ComprehendPiiAccessControlS3ObjectLambda
```

You can add or view this function on the AWS Management Console by using the following AWS Serverless Application Repository link: [ComprehendPiiAccessControlS3ObjectLambda](https://console.aws.amazon.com/lambda/home#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:839782855223:applications/ComprehendPiiAccessControlS3ObjectLambda)\.

To view this function on GitHub, see [Amazon Comprehend S3 Object Lambda](https://github.com/aws-samples/amazon-comprehend-s3-object-lambdas)\.

## Example 2: PII redaction<a name="olap-examples-2"></a>

This Lambda function uses Amazon Comprehend, a natural language processing \(NLP\) service that uses machine learning to find insights and relationships in text\. This function automatically redacts personally identifiable information \(PII\), such as names, addresses, dates, credit card numbers, and social security numbers from documents in your Amazon S3 bucket\. 

If you have documents in your bucket that include information such as credit card numbers or bank account information, you can configure the PII Redaction S3 Object Lambda function to detect PII and then return a copy of these documents in which PII entity types are redacted\.

To get started, deploy the following Lambda function in your account and add the ARN for the function to your Object Lambda access point configuration\.

The following is an example ARN for this function:

```
arn:aws:serverlessrepo:us-east-1:111122223333::applications/ComprehendPiiRedactionS3ObjectLambda
```

You can add or view this function on the AWS Management Console by using the following AWS Serverless Application Repository link: [ComprehendPiiRedactionS3ObjectLambda](https://console.aws.amazon.com/lambda/home#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:839782855223:applications/ComprehendPiiRedactionS3ObjectLambda)\.

To view this function on GitHub, see [Amazon Comprehend S3 Object Lambda](https://github.com/aws-samples/amazon-comprehend-s3-object-lambdas)\.

To learn about complete end\-to\-end procedures for some S3 Object Lambda tasks in PII redaction, see [Tutorial: Detecting and redacting PII data with S3 Object Lambda and Amazon Comprehend](tutorial-s3-object-lambda-redact-pii.md)\.

## Example 3: Decompression<a name="olap-examples-3"></a>

The Lambda function `S3ObjectLambdaDecompression` can decompress objects that are stored in Amazon S3 in one of six compressed file formats: `bzip2`, `gzip`, `snappy`, `zlib`, `zstandard`, and `ZIP`\. 

To get started, deploy the following Lambda function in your account and add the ARN for the function to your Object Lambda access point configuration\.

The following is an example ARN for this function:

```
arn:aws:serverlessrepo:us-east-1:111122223333::applications/S3ObjectLambdaDecompression
```

You can add or view this function on the AWS Management Console by using the following AWS Serverless Application Repository link: [S3ObjectLambdaDecompression](https://eu-west-1.console.aws.amazon.com/lambda/home?region=eu-west-1#/create/app?applicationId=arn:aws:serverlessrepo:eu-west-1:123065155563:applications/S3ObjectLambdaDecompression)\.

To view this function on GitHub, see [S3 Object Lambda Decompression](https://github.com/aws-samples/amazon-s3-object-lambda-decompression)\.