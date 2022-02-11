# Configuring IAM policies for Object Lambda access points<a name="olap-policies"></a>

Amazon S3 access points support AWS Identity and Access Management \(IAM\) resource policies that you can use to control the use of the access point by resource, user, or other conditions\. For step\-by\-step examples, see [Tutorial: Transforming data for your application with S3 Object Lambda](tutorial-s3-object-lambda-uppercase.md) and [Tutorial: Detecting and redacting PII data with S3 Object Lambda and Amazon Comprehend](tutorial-s3-object-lambda-redact-pii.md)\.

In the case of a single AWS account, the following four resources must have permissions granted to work with Object Lambda access points:
+ The IAM user or role
+ The bucket and associated standard access point
+ The Object Lambda access point
+ The AWS Lambda function

Make sure to resolve security warnings, errors, general warnings, and suggestions from AWS Identity and Access Management Access Analyzer before you save your policy\. IAM Access Analyzer runs policy checks to validate your policy against IAM [policy grammar](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_grammar.html) and [best practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)\. These checks generate findings and provide actionable recommendations to help you author policies that are functional and conform to security best practices\. To learn more about validating policies using IAM Access Analyzer, see [IAM Access Analyzer policy validation](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-policy-validation.html) in the *IAM User Guide*\. To view a list of the warnings, errors, and suggestions that are returned by IAM Access Analyzer, see [IAM Access Analyzer policy check reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-reference-policy-checks.html)\.

These examples assume that you have the following resources:
+ An Amazon S3 bucket with following Amazon Resource Name \(ARN\): 

  `arn:aws:s3:::DOC-EXAMPLE-BUCKET1`

  The following S3 bucket policy example delegates access control for a bucket to the bucket's access points\. This policy allows full access to all access points owned by the bucket owner's account\. Thus, all access to this bucket is controlled by the policies attached to its access points\. Users can read from the bucket only through the S3 Access Point, which means that operations can be invoked only through access points\. For more information, see [Delegating access control to access points](access-points-policies.md#access-points-delegating-control)\.   
**Example – bucket policy delegating access control to access points**  

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
+ An Amazon S3 standard access point on this bucket with the following ARN: 

  `arn:aws:s3:us-east-1:111122223333:accesspoint/my-access-point`
+ An Object Lambda access point with the following ARN: 

  `arn:aws:s3-object-lambda:us-east-1:111122223333:accesspoint/my-object-lambda-ap`
+ An AWS Lambda function with the following ARN: 

  `arn:aws:lambda:us-east-1:111122223333:function:MyObjectLambdaFunction`

**Note**  
If using a Lambda function from your account you must include the function version in your policy statement\. For example, `arn:aws:lambda:us-east-1:111122223333:function:MyObjectLambdaFunction:$LATEST`

The following IAM policy grants a user permission to the Lambda function, standard access point and the S3 Object Lambda access point\.

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

## Lambda execution role<a name="olap-execution-role"></a>

Your Lambda function needs permission to send data to S3 Object Lambda when requests are made to an Object Lambda access point\. This is provided by enabling the `s3-object-lambda:WriteGetObjectResponse` permission on your Lambda function's execution role\. You can create a new execution role or update an existing one\.

**To create an execution role in the IAM console**

1. Open the [Roles page](https://console.aws.amazon.com/iam/home#/roles) in the IAM console\.

1. Choose **Create role**\.

1. Under **Common use cases**, choose **Lambda**\.

1. Choose **Next: Permissions**\.

1. Under **Attach permissions policies**, choose the AWS managed policy [AmazonS3ObjectLambdaExecutionRolePolicy](https://console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/service-role/AmazonS3ObjectLambdaExecutionRolePolicy$serviceLevelSummary)\.

1. Choose **Next: Tags**\.

1. Choose **Next: Review**\.

1. For **Role name**, enter **s3\-object\-lambda\-role**\.

1. Choose **Create role**\.

1. Apply the newly created **s3\-object\-lambda\-role** as your Lambda function's execution role\.

For detailed instructions, see [Creating a role for an AWS service \(console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html#roles-creatingrole-service-console) in the *IAM User Guide*\.

**To update your Lambda function's execution role**

Add the following statement to the execution role that is used by the Lambda function\.

```
{
    "Sid": "AllowObjectLambdaAccess",
    "Action": ["s3-object-lambda:WriteGetObjectResponse"],
    "Effect": "Allow",
    "Resource": "*"
  }
```

For more information about execution roles, see [Lambda execution role](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html) in the *AWS Lambda Developer Guide*\.

## Using context keys with Object Lambda access points<a name="olap-keys"></a>

With S3 Object Lambda, GET requests will automatically invoke Lambda functions and all other requests will be forwarded to Amazon S3\. S3 Object Lambda will evaluate context keys such as `s3-object-lambda:TlsVersion` or `s3-object-lambda:AuthType` related to the connection or signing of the request\. All other context keys, such as ` s3:prefix`, are evaluated by Amazon S3\. 