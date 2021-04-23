# Using AWS built Lambda functions<a name="olap-examples"></a>

AWS provides some prebuilt Lambda functions that you can use with S3 Object Lambda to detect and redact personally identifiable information \(PII\) and decompress S3 objects\. These Lambda functions are available in the AWS Serverless Application Repository and can be selected through the AWS Management Console when you create your Object Lambda access point\. 

For more information on how to deploy severless applications from the AWS Serverless Application Repository, see [Deploying Applications](https://docs.aws.amazon.com/serverlessrepo/latest/devguide/serverlessrepo-consuming-applications.html) in the *AWS Serverless Application Repository Developer Guide*\.

## Example 1: PII Access Control<a name="olap-examples-1"></a>

This Lambda function uses Amazon Comprehend, a natural language processing \(NLP\) service using machine learning to find insights and relationships in text\. It automatically detects personally identifiable information \(PII\) such as names, addresses, dates, credit card numbers, and social security numbers from documents in your Amazon S3 bucket\. If you have documents in your bucket that include PII, you can configure the PII Access Control S3 Object Lambda function to detect these PII entity types and restrict access to unauthorized users\.

To get started, simply deploy the following Lambda function in your account and add the ARN in your Object Lambda access point configuration\.

ARN:

```
arn:aws:serverlessrepo:us-east-1:839782855223:applications/ComprehendPiiAccessControlS3ObjectLambda
```

You can add the view this function on the AWS Management Console using the following SAR link: [ComprehendPiiAccessControlS3ObjectLambda](https://console.aws.amazon.com/lambda/home#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:839782855223:applications/ComprehendPiiAccessControlS3ObjectLambda)\.

To view this function on GitHub see [Amazon Comprehend S3 Object Lambda](https://github.com/aws-samples/amazon-comprehend-s3-object-lambdas)\.

## Example 2: PII Redaction<a name="olap-examples-2"></a>

This Lambda function uses Amazon Comprehend, a natural language processing \(NLP\) service using machine learning to find insights and relationships in text\. It automatically redacts personally identifiable information \(PII\) such as names, addresses, dates, credit card numbers, and social security numbers from documents in your Amazon S3 bucket\. If you have documents in your bucket that include information such as credit card numbers or bank account information, you can configure the PII Redaction S3 Object Lambda function to detect PII and then return a copy of these documents in which PII entity types are redacted\.

To get started, simply deploy the following Lambda function in your account and add the ARN in your Object Lambda access point configuration\.

ARN:

```
arn:aws:serverlessrepo:us-east-1:839782855223:applications/ComprehendPiiRedactionS3ObjectLambda
```

You can add the view this function on the AWS Management Console using the following SAR link: [ComprehendPiiRedactionS3ObjectLambda](https://console.aws.amazon.com/lambda/home#/create/app?applicationId=arn:aws:serverlessrepo:us-east-1:839782855223:applications/ComprehendPiiRedactionS3ObjectLambda)\.

To view this function on GitHub see [Amazon Comprehend S3 Object Lambda](https://github.com/aws-samples/amazon-comprehend-s3-object-lambdas)\.

## Example 3: Decompression<a name="olap-examples-3"></a>

The Lambda function S3ObjectLambdaDecompression, is equipped to decompress objects stored in S3 in one of six compressed file formats including bzip2, gzip, snappy, zlib, zstandard and ZIP\. To get started, simply deploy the following Lambda function in your account and add the ARN in your Object Lambda access point configuration\.

ARN:

```
arn:aws:serverlessrepo:eu-west-1:123065155563:applications/S3ObjectLambdaDecompression
```

You can add the view this function on the AWS Management Console using the following SAR link: [S3ObjectLambdaDecompression](https://eu-west-1.console.aws.amazon.com/lambda/home?region=eu-west-1#/create/app?applicationId=arn:aws:serverlessrepo:eu-west-1:123065155563:applications/S3ObjectLambdaDecompression)\.

To view this function on GitHub see [S3 Object Lambda Decompression](https://github.com/aws-samples/amazon-s3-object-lambda-decompression)\.