# Creating Object Lambda Access Points<a name="olap-create"></a>

An Object Lambda access point is associated with exactly one standard access point and thus one Amazon S3 bucket\. To create an Object Lambda access point, you need the following resources:
+ An IAM policy
+ An Amazon S3 bucket
+ A standard S3 access point
+ An AWS Lambda function

The following sections describe how to create an Object Lambda access point using the AWS Management Console and AWS CLI\.

## Create an Object Lambda access point<a name="create-olap"></a>

For information about how to create an Object Lambda access point using the REST API, see [CreateAccessPointForObjectLambda](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_CreateAccessPointForObjectLambda.html) in the *Amazon Simple Storage Service API Reference*\.

### Using the S3 console<a name="olap-create-console"></a>

**To create an Object Lambda access point using the console**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane on the left side of the console, choose **Object Lambda access points**\.

1. On the **Object Lambda access points** page, choose **Create Object Lambda access point**\.

1. For **Object Lambda access point name**, enter the name you want to use for the access point\. 

   As with standard access points, there are rules for naming\. For more information, see [Rules for naming Amazon S3 access points](creating-access-points.md#access-points-names)\.

1. For **Supporting access point**, enter or browse to the standard access point that you want to use\. The access point must be in the same AWS Region as the objects you want to transform\.

1. For **Invoke Lambda function**, you can choose to use a prebuilt function or enter the Amazon Resource Name \(ARN\) of an AWS Lambda function in your AWS account\. 

   For more information about prebuilt functions, see [Using AWS built Lambda functions](olap-examples.md)\.

1. \(Optional\) For **Range and part number**, you must enable this option in order to process GET requests with range and part number headers\. Selecting this option confirms that your Lambda function is able to recognize and process these requests\. For more information about range headers and part numbers, see [Working with Range and partNumber headers](olap-writing-lambda.md#range-get-olap)\.

1. \(Optional\) Under **Payload**, add JSON text to provide your Lambda function with additional information\. A payload is optional JSON that you can provide to your Lambda function as input\. You can configure payloads with different parameters for different Object Lambda access points that invoke the same Lambda function, thereby extending the flexibility of your Lambda function\.

1. \(Optional\) For **Request metrics**, choose **enable** or **disable** to add Amazon S3 monitoring to your Object Lambda access point\. Request metrics are billed at the standard CloudWatch rate\.

1. \(Optional\) Under **Object Lambda access point policy**, set a resource policy\. This resource policy grants GetObject permission for the specified Object Lambda access point\.

1. Choose **Create Object Lambda access point**\.

### Using the AWS CLI<a name="olap-create-cli"></a>

**To create an Object Lambda access point using an AWS CloudFormation template**

1. Download the AWS Lambda function deployment package `s3objectlambda_deployment_package.zip` at [S3 Object Lambda default configuration](https://github.com/aws-samples/amazon-s3-object-lambda-default-configuration)\.

1. Upload the package to an Amazon S3 bucket that has object versioning enabled\.

   ```
   aws s3api put-object --bucket Amazon S3 bucket name --key s3objectlambda_deployment_package.zip --body release/s3objectlambda_deployment_package.zip
   ```

1. Download the AWS CloudFormation template `s3objectlambda_defaultconfig.yaml` at [S3 Object Lambda default configuration](https://github.com/aws-samples/amazon-s3-object-lambda-default-configuration)\.

1. Deploy the template to your AWS account\.

   ```
   aws cloudformation deploy --template-file s3objectlambda_defaultconfig.yaml \
    --stack-name AWS CloudFormation stack name --parameter-overrides ObjectLambdaAccessPointName=Object Lambda access point name \
     SupportingAccessPointName=Amazon S3 access point S3BucketName=Amazon S3 bucket \
     LambdaFunctionS3BucketName=Amazon S3 bucket containing your Lambda package LambdaFunctionS3Key=Lambda object key LambdaFunctionS3ObjectVersion=Lambda object version LambdaFunctionRuntime=Lambda function runtime --capabilities capability_IAM
   ```

For more information about modifying the AWS CloudFormation template's default configuration, see [Using an AWS CloudFormation template to automate S3 Object Lambda setup](olap-using-cfn-template.md)\.

**To create an Object Lambda access point using the AWS CLI**

The following example creates an Object Lambda access point named *`my-object-lambda-ap`* for the bucket *`DOC-EXAMPLE-BUCKET1`* in the account *`111122223333`*\. This example assumes that a standard access point named *`example-ap`* has already been created\. For information about creating a standard access point, see [Creating access points](creating-access-points.md)\.

This example uses the AWS prebuilt function `compress`\. For example AWS Lambda functions, see [Using AWS built Lambda functions](olap-examples.md)\.

1. Create a bucket\. In this example, we will use *`DOC-EXAMPLE-BUCKET1`*\. For information about creating buckets, see [Creating a bucket](create-bucket-overview.md)\.

1. Create a standard access point and attach it to your bucket\. In this example, we will use *`example-ap`*\. For information about creating standard access points, see [Creating access points](creating-access-points.md)

1. Create a Lambda function in your account that you would like to use to transform your Amazon S3 object\. See [Using Lambda with the AWS CLI](https://docs.aws.amazon.com/lambda/latest/dg/gettingstarted-awscli.html) in the *AWS Lambda Developer Guide*\. You can also use an AWS prebuilt Lambda function\.

1. Create a JSON configuration file named `my-olap-configuration.json`\. In this configuration, provide the supporting access point and Lambda function ARN created in the previous steps\.  
**Example**  

   

   ```
   {
       "SupportingAccessPoint" : "arn:aws:s3:us-east-1:111122223333:accesspoint/example-ap",
       "TransformationConfigurations": [{
           "Actions" : ["GetObject"],
           "ContentTransformation" : {
               "AwsLambda": {
                   "FunctionPayload" : "{\"compressionType\":\"gzip\"}",
                   "FunctionArn" : "arn:aws:lambda:us-east-1:111122223333:function/compress"
               }
           }
       }]
   }
   ```

1. Run `create-access-point-for-object-lambda` to create your Object Lambda access point\.

   ```
   aws s3control create-access-point-for-object-lambda --account-id 111122223333 --name my-object-lambda-ap --configuration file://my-olap-configuration.json
   ```

1. \(Optional\) Create a JSON policy file named `my-olap-policy.json`\.

   This resource policy grants `GetObject` permission for account *`444455556666`* to the specified Object Lambda access point\.  
**Example**  

   

   ```
   {
       "Version": "2008-10-17",
       "Statement": [
           {
               "Sid": "Grant account 444455556666 GetObject access",
               "Effect": "Allow",
               "Action": "s3-object-lambda:GetObject",
               "Principal": {
                   "AWS": "arn:aws:iam::444455556666:root"
               },
               "Resource": "your-object-lambda-access-point-arn"
           }
       ]
   }
   ```

1. \(Optional\) Run `put-access-point-policy-for-object-lambda` to set your resource policy\.

   ```
   aws s3control put-access-point-policy-for-object-lambda --account-id 111122223333 --name my-object-lambda-ap --policy file://my-olap-policy.json
   ```

1. \(Optional\) Specify a payload\.

   A payload is optional JSON that you can provide to your AWS Lambda function as input\. You can configure payloads with different parameters for different Object Lambda access points that invoke the same Lambda function, thereby extending the flexibility of your Lambda function\.

   The following Object Lambda access point configuration shows a payload with two parameters\.

   ```
   {
   	"SupportingAccessPoint": "AccessPointArn",
   	"CloudWatchMetricsEnabled": false,
   	"TransformationConfigurations": [{
   		"Actions": ["GetObject"],
   		"ContentTransformation": {
   			"AwsLambda": {
   				"FunctionArn": "FunctionArn",
   				"FunctionPayload": "{\"res-x\": \"100\",\"res-y\": \"100\"}"
   			}
   		}
   	}]
   }
   ```

   The following Object Lambda access point configuration shows a payload with one parameter, and with `GetObject-Range` and `GetObject-PartNumber` enabled\.

   ```
   {
       "SupportingAccessPoint":"AccessPointArn",
       "CloudWatchMetricsEnabled": false,
       "AllowedFeatures": ["GetObject-Range", "GetObject-PartNumber"],        
       "TransformationConfigurations": [{
           "Actions": ["GetObject"],
           "ContentTransformation": {
               "AwsLambda": {
                   "FunctionArn":"FunctionArn",
                   "FunctionPayload": "{\"compression-amount\": \"5\"}"
               }
           }
       }]
   }
   ```
**Important**  
When you're using Object Lambda access points, make sure that the payload does not contain any confidential information\.

### Using the AWS CloudFormation console and template<a name="olap-create-cfn-console"></a>

You can create an Object Lambda access point using the default configuration provided by Amazon S3\. You can download an AWS CloudFormation template and Lambda function source code from the [GitHub repository](https://github.com/aws-samples/amazon-s3-object-lambda-default-configuration) and deploy these resources to set up a functional Object Lambda access point\.

**To upload the Lambda function deployment package**

1. Download the AWS Lambda function deployment package *s3objectlambda\_deployment\_package\.zip* at [S3 Object Lambda default configuration](https://github.com/aws-samples/amazon-s3-object-lambda-default-configuration)\.

1. Upload the package to an Amazon S3 bucket that has object versioning enabled\.

**To create an Object Lambda access point using the AWS CloudFormation console**

1. Download the AWS CloudFormation template *s3objectlambda\_defaultconfig\.yaml* at [S3 Object Lambda default configuration](https://github.com/aws-samples/amazon-s3-object-lambda-default-configuration)\.

1. Sign in to the AWS Management Console and open the AWS CloudFormation console at [https://console\.aws\.amazon\.com/cloudformation](https://console.aws.amazon.com/cloudformation/)\.

1. On the AWS CloudFormation page, choose **Create stack**\.

1. For **Prerequisite \- Prepare template**, choose **Template is ready**\.

1. For **Specify template**, choose **Upload a template file** and upload *s3objectlambda\_defaultconfig\.yaml*\.

1. Choose **Next**\.

1. On the **Specify stack details** page, enter a name for the stack\.

1. Choose **Next**\.

1. In the Parameters section, specify the following parameters that are defined in the stack template:

   1. Enter the Amazon S3 bucket name where you uploaded the deployment package for **LambdaFunctionS3BucketName**\.

   1. Enter the Amazon S3 object key where you uploaded the deployment package for **LambdaFunctionS3Key**\.

   1. Enter the Amazon S3 object version where you uploaded the deployment package for **LambdaFunctionS3ObjectVersion**\.

   1. Enter your preferred runtime for the Lambda function for **LambdaFunctionRuntime**\. The available choices are `nodejs14.x`, `python3.9`, `java11`\.

   1. Enter a name for your Object Lambda access point for **ObjectLambdaAccessPointName**\.

   1. Enter the Amazon S3 bucket name that will be associated with your Object Lambda access point for **S3BucketName**\.

   1. Enter the name of your supporting access point for **SupportingAccessPointName**\.
**Note**  
This is an access point associated with the Amazon S3 bucket that you chose in the previous step\. If you do not have any access points associated with your Amazon S3 bucket, you can configure the template to create one for you by selecting **true** for **CreateNewSupportingAccessPoint**\.

1. Choose **Next**\.

1. On the **Configure stack options page**, choose **Next**\.

   For more information on the optional settings on this page, see [Setting AWS CloudFormation stack options](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-add-tags.html) in the *AWS CloudFormation User Guide*\.

1. On the **Review** page, choose **Create stack**\.

For more information about modifying the AWS CloudFormation template's default configuration, see [Using an AWS CloudFormation template to automate S3 Object Lambda setup](olap-using-cfn-template.md)\.

For more information about configuring Object Lambda access points using AWS CloudFormation without the template, see [AWS::S3ObjectLambda::AccessPoint](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-s3objectlambda-accesspoint.html) in the *AWS CloudFormation User Guide*\.

### Using AWS Cloud Development Kit \(CDK\)<a name="olap-create-cdk"></a>

For more information about configuring Object Lambda access points using the AWS CDK, see [AWS::S3ObjectLambda Construct Library](https://docs.aws.amazon.com/cdk/api/latest/docs/aws-s3objectlambda-readme.html) in the *AWS Cloud Development Kit \(CDK\) API Reference*\.