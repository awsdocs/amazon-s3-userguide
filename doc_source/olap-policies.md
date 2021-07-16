# Configuring IAM policies for Object Lambda access points<a name="olap-policies"></a>

S3 access points support AWS Identity and Access Management \(IAM\) resource policies that allow you to control the use of the access point by resource, user, or other conditions\.

In the case of a single AWS account, the following four resources must have permissions granted to work with Object Lambda access points:
+ The IAM user or role
+ The bucket and associated standard access point
+ The Object Lambda access point
+ The AWS Lambda function

These examples assume that you have the following resources:
+ An Amazon S3 bucket with permissions delegated to your access point\. For more information, see [Delegating access control to access points](access-points-policies.md#access-points-delegating-control)\.

  The bucket has the following Amazon Resource Name \(ARN\): 

  `arn:aws:s3:::DOC-EXAMPLE-BUCKET1`
+ An Amazon S3 standard access point on this bucket with the following ARN: 

  `arn:aws:s3:us-east-1:111122223333:accesspoint/my-access-point`
+ An Object Lambda access point with the following ARN: 

  `arn:aws:s3-object-lambda:us-east-1:111122223333:accesspoint/my-object-lambda-ap`
+ An AWS Lambda function with the following ARN: 

  `arn:aws:lambda:us-east-1:111122223333:function/MyObjectLambdaFunction`

**Note**  
If using a Lambda function from your account you must include the function version in your policy statement\. For example, `arn:aws:lambda:us-east-1:111122223333:function/MyObjectLambdaFunction:$LATEST`

The following IAM policy grants a user permission to interact with these resources\.

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
      "Resource": "arn:aws:lambda:us-east-1:111122223333:function/MyObjectLambdaFunction:$LATEST",
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
        "s3: Get*",
        "s3: List*"
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

Your AWS Lambda function needs permission to call back to the Object Lambda access point with the `WriteGetObjectResponse`\. Add the following statement to the `Execution` role that is used by the Lambda function\.

```
{
  {
    "Sid": "AllowObjectLambdaAccess",
    "Action": ["s3-object-lambda:WriteGetObjectResponse"],
    "Effect": "Allow",
    "Resource": "*"
  }
```

## Using context keys with Object Lambda access points<a name="olap-keys"></a>

With S3 Object Lambda, GET requests will automatically invoke Lambda functions and all other requests will be forwarded to S3\. S3 Object Lambda will evaluate context keys such as `s3-object-lambda:TlsVersion` or `s3-object-lambda:AuthType` related to the connection or signing of the request\. All other context keys, such as ` s3:prefix`, are evaluated by S3\. 