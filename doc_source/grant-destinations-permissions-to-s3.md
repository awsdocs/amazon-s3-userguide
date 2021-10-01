# Granting permissions to publish event notification messages to a destination<a name="grant-destinations-permissions-to-s3"></a>

Before Amazon S3 can publish event notification messages to a destination, you must grant the Amazon S3 principal the necessary permissions to call the relevant API to publish messages to an SNS topic, an SQS queue, or a Lambda function\. 

**Topics**
+ [Granting permissions to invoke an AWS Lambda function](#grant-lambda-invoke-permission-to-s3)
+ [Granting permissions to publish messages to an SNS topic or an SQS queue](#grant-sns-sqs-permission-for-s3)

## Granting permissions to invoke an AWS Lambda function<a name="grant-lambda-invoke-permission-to-s3"></a>

Amazon S3 publishes event messages to AWS Lambda by invoking a Lambda function and providing the event message as an argument\.

When you use the Amazon S3 console to configure event notifications on an Amazon S3 bucket for a Lambda function, the console sets up the necessary permissions on the Lambda function so that Amazon S3 has permissions to invoke the function from the bucket\. For more information, see [Enabling and configuring event notifications using the Amazon S3 console](enable-event-notifications.md)\. 

You can also grant Amazon S3 permissions from AWS Lambda to invoke your Lambda function\. For more information, see [Tutorial: Using AWS Lambda with Amazon S3](https://docs.aws.amazon.com/lambda/latest/dg/with-s3-example.html) in the *AWS Lambda Developer Guide*\.

## Granting permissions to publish messages to an SNS topic or an SQS queue<a name="grant-sns-sqs-permission-for-s3"></a>

To grant Amazon S3 permissions to publish messages to the SNS topic or SQS queue, you attach an AWS Identity and Access Management \(IAM\) policy to the destination SNS topic or SQS queue\. 

For an example of how to attach a policy to an SNS topic or an SQS queue, see [Walkthrough: Configuring a bucket for notifications \(SNS topic or SQS queue\)](ways-to-add-notification-config-to-bucket.md)\. For more information about permissions, see the following topics:
+ [Example cases for Amazon SNS access control](https://docs.aws.amazon.com/sns/latest/dg/AccessPolicyLanguage_UseCases_Sns.html) in the *Amazon Simple Notification Service Developer Guide*
+ [Identity and access management in Amazon SQS](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/UsingIAM.html) in the *Amazon Simple Queue Service Developer Guide*

### IAM policy for a destination SNS topic<a name="sns-topic-policy"></a>

The following is an example of an AWS Identity and Access Management \(IAM\) policy that you attach to the destination SNS topic\. For more information about how to use this policy to set up a destination Amazon SNS topic for event notifications, see [Walkthrough: Configuring a bucket for notifications \(SNS topic or SQS queue\)](ways-to-add-notification-config-to-bucket.md)\.

To use this policy, you must update the Amazon SNS topic ARN, bucket name, and bucket owner's AWS account ID\.

```
{
 "Version": "2012-10-17",
 "Id": "example-ID",
 "Statement": [
  {
   "Sid": "example-statement-ID",
   "Effect": "Allow",
   "Principal": {
     "Service": "s3.amazonaws.com"  
   },
   "Action": [
    "SNS:Publish"
   ],
   "Resource": "arn:aws:sns:Region:account-id:topic-name",
   "Condition": {
      "ArnLike": { "aws:SourceArn": "arn:aws:s3:::awsexamplebucket1" },
      "StringEquals": { "aws:SourceAccount": "bucket-owner-account-id" }
   }
  }
 ]
}
```

### IAM policy for a destination SQS queue<a name="sqs-queue-policy"></a>

The following is an example of an IAM policy that you attach to the destination SQS queue\. For more information about how to use this policy to set up a destination Amazon SQS queue for event notifications, see [Walkthrough: Configuring a bucket for notifications \(SNS topic or SQS queue\)](ways-to-add-notification-config-to-bucket.md)\.

To use this policy, you must update the Amazon SQS queue ARN, bucket name, and bucket owner's AWS account ID\.

```
{
 "Version": "2012-10-17",
 "Id": "example-ID",
 "Statement": [
  {
   "Sid": "example-statement-ID",
   "Effect": "Allow",
   "Principal": {
     "Service": "s3.amazonaws.com"  
   },
   "Action": [
    "SQS:SendMessage"
   ],
   "Resource": "arn:aws:sqs:Region:account-id:queue-name",
   "Condition": {
      "ArnLike": { "aws:SourceArn": "arn:aws:s3:*:*:awsexamplebucket1" },
      "StringEquals": { "aws:SourceAccount": "bucket-owner-account-id" }
   }
  }
 ]
}
```

Note that for both the Amazon SNS and Amazon SQS IAM policies, you can specify the `StringLike` condition in the policy, instead of the `ArnLike` condition\.

```
"Condition": {         
  "StringLike": { "aws:SourceArn": "arn:aws:s3:*:*:bucket-name" }
  }
```

### AWS KMS key policy<a name="key-policy-sns-sqs"></a>

If the SQS queue or SNS topics are encrypted with an AWS Key Management Service \(AWS KMS\) customer managed key, you must grant the Amazon S3 service principal permission to work with the encrypted topics or queue\. To grant the Amazon S3 service principal permission, add the following statement to the key policy for the customer managed key\.

```
{
    "Version": "2012-10-17",
    "Id": "example-ID",
    "Statement": [
        {
            "Sid": "example-statement-ID",
            "Effect": "Allow",
            "Principal": {
                "Service": "s3.amazonaws.com"
            },
            "Action": [
                "kms:GenerateDataKey",
                "kms:Decrypt"
            ],
            "Resource": "*",
            "Condition":{
              "StringEquals":{
                "aws:SourceAccount":"source-account-id"
              },
              "ArnLike":{
                "aws:SourceARN": "arn:aws:s3:::source-bucket-name"
              }
           }
        }
    ]
}
```

For more information about AWS KMS key policies, see [Using key policies in AWS KMS](https://docs.aws.amazon.com/kms/latest/developerguide/key-policies.html) in the *AWS Key Management Service Developer Guide*\. 

For more information about using server\-side encryption with AWS KMS for Amazon SQS and Amazon SNS, see the following:
+ [Key management](https://docs.aws.amazon.com/sns/latest/dg/sns-key-management.html) in the *Amazon Simple Notification Service Developer Guide*\.
+ [Key management](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/sqs-key-management.html) in the *Amazon Simple Queue Service Developer Guide*\.
+ [Encrypting messages published to Amazon SNS with AWS KMS](http://aws.amazon.com/blogs/compute/encrypting-messages-published-to-amazon-sns-with-aws-kms/) in the *AWS Compute Blog*\.