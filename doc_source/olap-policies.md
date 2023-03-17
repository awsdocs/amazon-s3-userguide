# Configuring IAM policies for Object Lambda Access Points<a name="olap-policies"></a>

Amazon S3 access points support AWS Identity and Access Management \(IAM\) resource policies that you can use to control the use of the access point by resource, user, or other conditions\. You can control access through an optional resource policy on your Object Lambda Access Point, or a resource policy on supporting access point\. For step\-by\-step examples, see [Tutorial: Transforming data for your application with S3 Object Lambda](tutorial-s3-object-lambda-uppercase.md) and [Tutorial: Detecting and redacting PII data with S3 Object Lambda and Amazon Comprehend](tutorial-s3-object-lambda-redact-pii.md)\. 

The following four resources must have permissions granted to work with Object Lambda Access Points:
+ The IAM identity, such as user or role\. For more information about IAM identities and best practices, see [IAM identities \(users, user groups, and roles\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html) in the *IAM User Guide*\.
+ The bucket and its associated standard access point\. When you're working with Object Lambda Access Points, this standard access point is known as a *supporting access point*\.
+ The Object Lambda Access Point\.
+ The AWS Lambda function\.

**Important**  
Before you save your policy, make sure to resolve security warnings, errors, general warnings, and suggestions from AWS Identity and Access Management Access Analyzer\. IAM Access Analyzer runs policy checks to validate your policy against IAM [policy grammar](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_grammar.html) and [best practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)\. These checks generate findings and provide actionable recommendations to help you author policies that are functional and conform to security best practices\.   
To learn more about validating policies by using IAM Access Analyzer, see [IAM Access Analyzer policy validation](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-policy-validation.html) in the *IAM User Guide*\. To view a list of the warnings, errors, and suggestions that are returned by IAM Access Analyzer, see [IAM Access Analyzer policy check reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-reference-policy-checks.html)\.

The following policy examples assume that you have the following resources:
+ An Amazon S3 bucket with the following Amazon Resource Name \(ARN\): 

  `arn:aws:s3:::DOC-EXAMPLE-BUCKET1`
+ An Amazon S3 standard access point on this bucket with the following ARN: 

  `arn:aws:s3:us-east-1:111122223333:accesspoint/my-access-point`
+ An Object Lambda Access Point with the following ARN: 

  `arn:aws:s3-object-lambda:us-east-1:111122223333:accesspoint/my-object-lambda-ap`
+ An AWS Lambda function with the following ARN: 

  `arn:aws:lambda:us-east-1:111122223333:function:MyObjectLambdaFunction`

**Note**  
If you're using a Lambda function from your account, you must include the function version in your policy statement\. In the following example ARN, the version is indicated by `$LATEST`:   
`arn:aws:lambda:us-east-1:111122223333:function:MyObjectLambdaFunction:$LATEST`  
For more information about Lambda function versions, see [Lambda function versions](https://docs.aws.amazon.com/lambda/latest/dg/configuration-versions.html) in the *AWS Lambda Developer Guide*\.

**Example – Bucket policy that delegates access control to standard access points**  
The following S3 bucket policy example delegates access control for a bucket to the bucket's standard access points\. This policy allows full access to all access points that are owned by the bucket owner's account\. Thus, all access to this bucket is controlled by the policies that are attached to its access points\. Users can read from the bucket only through an access point, which means that operations can be invoked only through access points\. For more information, see [Delegating access control to access points](access-points-policies.md#access-points-delegating-control)\.   

```
{
    "Version": "2012-10-17",
    "Statement" : [
    {
        "Effect": "Allow",
        "Principal" : { "AWS":"account-ARN"},
        "Action" : "*",
        "Resource" : [
            "arn:aws:s3:::DOC-EXAMPLE-BUCKET1", 
            "arn:aws:s3:::DOC-EXAMPLE-BUCKET1/*"
        ],
        "Condition": {
            "StringEquals" : { "s3:DataAccessPointAccount" : "Bucket owner's account ID" }
        }
    }]
}
```

**Example – Bucket policy that grants a user the necessary permissions to use an Object Lambda Access Point**  
The following IAM policy grants a user permissions to the Lambda function, the standard access point, and the Object Lambda Access Point\.  

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowLambdaInvocation",
      "Action": [
        "lambda:InvokeFunction"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:lambda:us-east-1:111122223333:function:MyObjectLambdaFunction:$LATEST",
      "Condition": {
        "ForAnyValue:StringEquals": {
          "aws:CalledVia": [
            "s3-object-lambda.amazonaws.com"
          ]
        }
      }
    },
    {
      "Sid": "AllowStandardAccessPointAccess",
      "Action": [
        "s3:Get*",
        "s3:List*"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:s3:us-east-1:111122223333:accesspoint/my-access-point/*",
      "Condition": {
        "ForAnyValue:StringEquals": {
          "aws:CalledVia": [
            "s3-object-lambda.amazonaws.com"
          ]
        }
      }
    },
    {
      "Sid": "AllowObjectLambdaAccess",
      "Action": [
        "s3-object-lambda:Get*",
        "s3-object-lambda:List*"
      ],
      "Effect": "Allow",
      "Resource": "arn:aws:s3-object-lambda:us-east-1:111122223333:accesspoint/my-object-lambda-ap"
    }
  ]
}
```

## Enable permissions for Lambda execution roles<a name="olap-execution-role"></a>

When `GET` requests are made to an Object Lambda Access Point, your Lambda function needs permission to send data to S3 Object Lambda\. This permission is provided by enabling the `s3-object-lambda:WriteGetObjectResponse` permission on your Lambda function's execution role\. You can create a new execution role or update an existing one\.

**Note**  
Your function needs the `s3-object-lambda:WriteGetObjectResponse` permission only if you're making a `GET` request\.

**To create an execution role in the IAM console**

1. Open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the left navigation pane, choose **Roles**\. 

1. Choose **Create role**\.

1. Under **Common use cases**, choose **Lambda**\.

1. Choose **Next**\.

1. On the **Add permissions** page, search for the AWS managed policy [https://console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/service-role/AmazonS3ObjectLambdaExecutionRolePolicy$serviceLevelSummary](https://console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/service-role/AmazonS3ObjectLambdaExecutionRolePolicy$serviceLevelSummary), and then select the check box beside the policy name\. 

   This policy should contain the `s3-object-lambda:WriteGetObjectResponse` Action\.

1. Choose **Next**\.

1. On the **Name, review, and create** page, for **Role name**, enter **s3\-object\-lambda\-role**\.

1. \(Optional\) Add a description and tags for this role\. 

1. Choose **Create role**\.

1. Apply the newly created **s3\-object\-lambda\-role** as your Lambda function's execution role\. This can be done during or after Lambda function creation in the Lambda console\.

For more information about execution roles, see [Lambda execution role](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html) in the *AWS Lambda Developer Guide*\.

## Using context keys with Object Lambda Access Points<a name="olap-keys"></a>

S3 Object Lambda will evaluate context keys such as `s3-object-lambda:TlsVersion` or `s3-object-lambda:AuthType` that are related to the connection or signing of the request\. All other context keys, such as `s3:prefix`, are evaluated by Amazon S3\. 