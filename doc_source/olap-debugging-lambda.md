# Debugging S3 Object Lambda<a name="olap-debugging-lambda"></a>

Requests to Amazon S3 Object Lambda access points might result in a new error response when something goes wrong with the Lambda function invocation or execution\. These errors follow the same format as standard Amazon S3 errors\. For information about S3 Object Lambda errors, see [S3 Object Lambda Error Code List](https://docs.aws.amazon.com/AmazonS3/latest/API/ErrorResponses.html#S3ObjectLambdaErrorCodeList) in the *Amazon Simple Storage Service API Reference*\.

For more information about general Lambda function debugging, see [Monitoring and troubleshooting Lambda applications](https://docs.aws.amazon.com/lambda/latest/dg/lambda-monitoring.html ) in the *AWS Lambda Developer Guide*\.

For information about standard Amazon S3 errors, see [Error Responses](https://docs.aws.amazon.com/AmazonS3/latest/API/ErrorResponses.html) in the *Amazon Simple Storage Service API Reference*\.

You can enable request metrics in Amazon CloudWatch for your Object Lambda Access Points\. These metrics help you monitor the operational performance of your access point\. You can enable request metrics during or after creation of your Object Lambda Access Point\. For more information, see [S3 Object Lambda request metrics in CloudWatch](metrics-dimensions.md#olap-cloudwatch-metrics)\.

To get more granular logging about requests made to your Object Lambda Access Points, you can enable AWS CloudTrail data events\. For more information, see [Logging data events for trails](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-data-events-with-cloudtrail.html) in the *AWS CloudTrail User Guide*\.