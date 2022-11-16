# Invoke AWS Lambda function<a name="batch-ops-invoke-lambda"></a>

The **Invoke AWS Lambda function** initiates AWS Lambda functions to perform custom actions on objects that are listed in a manifest\. This section describes how to create a Lambda function to use with S3 Batch Operations and how to create a job to invoke the function\. The S3 Batch Operations job uses the `LambdaInvoke` operation to run a Lambda function on every object listed in a manifest\.

You can work with S3 Batch Operations for Lambda using the AWS Management Console, AWS Command Line Interface \(AWS CLI\), AWS SDKs, or REST APIs\. For more information about using Lambda, see [ Getting Started with AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/getting-started.html) in the *AWS Lambda Developer Guide*\. 

The following sections explain how you can get started using S3 Batch Operations with Lambda\.

**Topics**
+ [Using Lambda with Amazon S3 batch operations](#batch-ops-invoke-lambda-using)
+ [Creating a Lambda function to use with S3 Batch Operations](#batch-ops-invoke-lambda-custom-functions)
+ [Creating an S3 Batch Operations job that invokes a Lambda function](#batch-ops-invoke-lambda-create-job)
+ [Providing task\-level information in Lambda manifests](#storing-task-level-information-in-lambda)
+ [Learning from S3 Batch Operations tutorial](#batch-ops-tutorials-lambda)

## Using Lambda with Amazon S3 batch operations<a name="batch-ops-invoke-lambda-using"></a>

When using S3 Batch Operations with AWS Lambda, you must create new Lambda functions specifically for use with S3 Batch Operations\. You can't reuse existing Amazon S3 event\-based functions with S3 Batch Operations\. Event functions can only receive messages; they don't return messages\. The Lambda functions that are used with S3 Batch Operations must accept and return messages\. For more information about using Lambda with Amazon S3 events, see [Using AWS Lambda with Amazon S3](https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html) in the *AWS Lambda Developer Guide*\.

You create an S3 Batch Operations job that invokes your Lambda function\. The job runs the same Lambda function on all of the objects listed in your manifest\. You can control what versions of your Lambda function to use while processing the objects in your manifest\. S3 Batch Operations support unqualified Amazon Resource Names \(ARNs\), aliases, and specific versions\. For more information, see [ Introduction to AWS Lambda Versioning](https://docs.aws.amazon.com/lambda/latest/dg/versioning-intro.html) in the *AWS Lambda Developer Guide*\.

If you provide the S3 Batch Operations job with a function ARN that uses an alias or the `$LATEST` qualifier, and you update the version that either of those points to, S3 Batch Operations starts calling the new version of your Lambda function\. This can be useful when you want to update functionality part of the way through a large job\. If you don't want S3 Batch Operations to change the version that is used, provide the specific version in the `FunctionARN` parameter when you create your job\.

### Response and result codes<a name="batch-ops-invoke-lambda-response-codes"></a>

There are two levels of codes that S3 Batch Operations expect from Lambda functions\. The first is the response code for the entire request, and the second is a per\-task result code\. The following table contains the response codes\.


****  

| Response code | Description | 
| --- | --- | 
| Succeeded | The task completed normally\. If you requested a job completion report, the task's result string is included in the report\. | 
| TemporaryFailure | The task suffered a temporary failure and will be redriven before the job completes\. The result string is ignored\. If this is the final redrive, the error message is included in the final report\. | 
| PermanentFailure | The task suffered a permanent failure\. If you requested a job\-completion report, the task is marked as Failed and includes the error message string\. Result strings from failed tasks are ignored\. | 

## Creating a Lambda function to use with S3 Batch Operations<a name="batch-ops-invoke-lambda-custom-functions"></a>

This section provides example AWS Identity and Access Management \(IAM\) permissions that you must use with your Lambda function\. It also contains an example Lambda function to use with S3 Batch Operations\. If you have never created a Lambda function before, see [Tutorial: Using AWS Lambda with Amazon S3](https://docs.aws.amazon.com/lambda/latest/dg/with-s3-example.html) in the *AWS Lambda Developer Guide*\.

You must create Lambda functions specifically for use with S3 Batch Operations\. You can't reuse existing Amazon S3 event\-based Lambda functions\. This is because Lambda functions that are used for S3 Batch Operations must accept and return special data fields\. 

**Important**  
AWS Lambda functions written in Java accept either [RequestHandler](https://github.com/aws/aws-lambda-java-libs/blob/master/aws-lambda-java-core/src/main/java/com/amazonaws/services/lambda/runtime/RequestHandler.java) or [RequestStreamHandler](https://github.com/aws/aws-lambda-java-libs/blob/master/aws-lambda-java-core/src/main/java/com/amazonaws/services/lambda/runtime/RequestStreamHandler.java) handler interfaces\. However, to support S3 Batch Operations request and response format, AWS Lambda requires the `RequestStreamHandler` interface for custom serialization and deserialization of a request and response\. This interface allows Lambda to pass an InputStream and OutputStream to the Java `handleRequest` method\.   
Be sure to use the `RequestStreamHandler` interface when using Lambda functions with S3 Batch Operations\. If you use a `RequestHandler` interface, the batch job will fail with "Invalid JSON returned in Lambda payload" in the completion report\.   
For more information, see [Handler interfaces](https://docs.aws.amazon.com/lambda/latest/dg/java-handler.html#java-handler-interfaces) in the *AWS Lambda User Guide*\.

### Example IAM permissions<a name="batch-ops-invoke-lambda-custom-functions-iam"></a>

The following are examples of the IAM permissions that are necessary to use a Lambda function with S3 Batch Operations\. 

**Example — S3 Batch Operations trust policy**  
The following is an example of the trust policy that you can use for the Batch Operations IAM role\. This IAM role is specified when you create the job and gives Batch Operations permission to assume the IAM role\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "batchoperations.s3.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```

**Example — Lambda IAM policy**  
The following is an example of an IAM policy that gives S3 Batch Operations permission to invoke the Lambda function and read the input manifest\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "BatchOperationsLambdaPolicy",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:GetObjectVersion",
                "s3:PutObject",
                "lambda:InvokeFunction"
            ],
            "Resource": "*"
        }
    ]
}
```

### Example request and response<a name="batch-ops-invoke-lambda-custom-functions-request"></a>

This section provides request and response examples for the Lambda function\.

**Example Request**  
The following is a JSON example of a request for the Lambda function\.  

```
{
    "invocationSchemaVersion": "1.0",
    "invocationId": "YXNkbGZqYWRmaiBhc2RmdW9hZHNmZGpmaGFzbGtkaGZza2RmaAo",
    "job": {
        "id": "f3cc4f60-61f6-4a2b-8a21-d07600c373ce"
    },
    "tasks": [
        {
            "taskId": "dGFza2lkZ29lc2hlcmUK",
            "s3Key": "customerImage1.jpg",
            "s3VersionId": "1",
            "s3BucketArn": "arn:aws:s3:us-east-1:0123456788:awsexamplebucket1"
        }
    ]
}
```

**Example Response**  
The following is a JSON example of a response for the Lambda function\.  

```
{
  "invocationSchemaVersion": "1.0",
  "treatMissingKeysAs" : "PermanentFailure",
  "invocationId" : "YXNkbGZqYWRmaiBhc2RmdW9hZHNmZGpmaGFzbGtkaGZza2RmaAo",
  "results": [
    {
      "taskId": "dGFza2lkZ29lc2hlcmUK",
      "resultCode": "Succeeded",
      "resultString": "[\"Mary Major", \"John Stiles\"]"
    }
  ]
}
```

### Example Lambda function for S3 Batch Operations<a name="batch-ops-invoke-lambda-custom-functions-example"></a>

The following example Python Lambda removes a delete marker from a versioned object\.

As the example shows, keys from S3 Batch Operations are URL encoded\. To use Amazon S3 with other AWS services, it's important that you URL decode the key that is passed from S3 Batch Operations\.

```
import logging
from urllib import parse
import boto3
from botocore.exceptions import ClientError

logger = logging.getLogger(__name__)
logger.setLevel('INFO')

s3 = boto3.client('s3')


def lambda_handler(event, context):
    """
    Removes a delete marker from the specified versioned object.

    :param event: The S3 batch event that contains the ID of the delete marker
                  to remove.
    :param context: Context about the event.
    :return: A result structure that Amazon S3 uses to interpret the result of the
             operation. When the result code is TemporaryFailure, S3 retries the
             operation.
    """
    # Parse job parameters from Amazon S3 batch operations
    invocation_id = event['invocationId']
    invocation_schema_version = event['invocationSchemaVersion']

    results = []
    result_code = None
    result_string = None

    task = event['tasks'][0]
    task_id = task['taskId']

    try:
        obj_key = parse.unquote(task['s3Key'], encoding='utf-8')
        obj_version_id = task['s3VersionId']
        bucket_name = task['s3BucketArn'].split(':')[-1]

        logger.info("Got task: remove delete marker %s from object %s.",
                    obj_version_id, obj_key)

        try:
            # If this call does not raise an error, the object version is not a delete
            # marker and should not be deleted.
            response = s3.head_object(
                Bucket=bucket_name, Key=obj_key, VersionId=obj_version_id)
            result_code = 'PermanentFailure'
            result_string = f"Object {obj_key}, ID {obj_version_id} is not " \
                            f"a delete marker."

            logger.debug(response)
            logger.warning(result_string)
        except ClientError as error:
            delete_marker = error.response['ResponseMetadata']['HTTPHeaders'] \
                .get('x-amz-delete-marker', 'false')
            if delete_marker == 'true':
                logger.info("Object %s, version %s is a delete marker.",
                            obj_key, obj_version_id)
                try:
                    s3.delete_object(
                        Bucket=bucket_name, Key=obj_key, VersionId=obj_version_id)
                    result_code = 'Succeeded'
                    result_string = f"Successfully removed delete marker " \
                                    f"{obj_version_id} from object {obj_key}."
                    logger.info(result_string)
                except ClientError as error:
                    # Mark request timeout as a temporary failure so it will be retried.
                    if error.response['Error']['Code'] == 'RequestTimeout':
                        result_code = 'TemporaryFailure'
                        result_string = f"Attempt to remove delete marker from  " \
                                        f"object {obj_key} timed out."
                        logger.info(result_string)
                    else:
                        raise
            else:
                raise ValueError(f"The x-amz-delete-marker header is either not "
                                 f"present or is not 'true'.")
    except Exception as error:
        # Mark all other exceptions as permanent failures.
        result_code = 'PermanentFailure'
        result_string = str(error)
        logger.exception(error)
    finally:
        results.append({
            'taskId': task_id,
            'resultCode': result_code,
            'resultString': result_string
        })
    return {
        'invocationSchemaVersion': invocation_schema_version,
        'treatMissingKeysAs': 'PermanentFailure',
        'invocationId': invocation_id,
        'results': results
    }
```

## Creating an S3 Batch Operations job that invokes a Lambda function<a name="batch-ops-invoke-lambda-create-job"></a>

When creating an S3 Batch Operations job to invoke a Lambda function, you must provide the following:
+ The ARN of your Lambda function \(which might include the function alias or a specific version number\)
+ An IAM role with permission to invoke the function
+ The action parameter `LambdaInvokeFunction`

For more information about creating an S3 Batch Operations job, see [Creating an S3 Batch Operations job](batch-ops-create-job.md) and [Operations supported by S3 Batch Operations](batch-ops-operations.md)\.

The following example creates an S3 Batch Operations job that invokes a Lambda function using the AWS CLI\.

```
aws s3control create-job
    --account-id <AccountID>
    --operation  '{"LambdaInvoke": { "FunctionArn": "arn:aws:lambda:Region:AccountID:function:LambdaFunctionName" } }'
    --manifest '{"Spec":{"Format":"S3BatchOperations_CSV_20180820","Fields":["Bucket","Key"]},"Location":{"ObjectArn":"arn:aws:s3:::ManifestLocation","ETag":"ManifestETag"}}'
    --report '{"Bucket":"arn:aws:s3:::awsexamplebucket1","Format":"Report_CSV_20180820","Enabled":true,"Prefix":"ReportPrefix","ReportScope":"AllTasks"}'
    --priority 2
    --role-arn arn:aws:iam::AccountID:role/BatchOperationsRole
    --region Region
    --description “Lambda Function"
```

## Providing task\-level information in Lambda manifests<a name="storing-task-level-information-in-lambda"></a>

When you use AWS Lambda functions with S3 Batch Operations, you might want additional data to accompany each task/key that is operated on\. For example, you might want to have both a source object key and new object key provided\. Your Lambda function could then copy the source key to a new S3 bucket under a new name\. By default, Amazon S3 batch operations let you specify only the destination bucket and a list of source keys in the input manifest to your job\. The following describes how you can include additional data in your manifest so that you can run more complex Lambda functions\.

To specify per\-key parameters in your S3 Batch Operations manifest to use in your Lambda function's code, use the following URL\-encoded JSON format\. The `key` field is passed to your Lambda function as if it were an Amazon S3 object key\. But it can be interpreted by the Lambda function to contain other values or multiple keys, as shown following\. 

**Note**  
The maximum number of characters for the `key` field in the manifest is 1,024\.

**Example — manifest substituting the "Amazon S3 keys" with JSON strings**  
The URL\-encoded version must be provided to S3 Batch Operations\.  

```
my-bucket,{"origKey": "object1key", "newKey": "newObject1Key"}
my-bucket,{"origKey": "object2key", "newKey": "newObject2Key"}
my-bucket,{"origKey": "object3key", "newKey": "newObject3Key"}
```

**Example — manifest URL\-encoded**  
This URL\-encoded version must be provided to S3 Batch Operations\. The non\-URL\-encoded version does not work\.  

```
my-bucket,%7B%22origKey%22%3A%20%22object1key%22%2C%20%22newKey%22%3A%20%22newObject1Key%22%7D
my-bucket,%7B%22origKey%22%3A%20%22object2key%22%2C%20%22newKey%22%3A%20%22newObject2Key%22%7D
my-bucket,%7B%22origKey%22%3A%20%22object3key%22%2C%20%22newKey%22%3A%20%22newObject3Key%22%7D
```

**Example — Lambda function with manifest format writing results to the job report**  
 This Lambda function shows how to parse a pipe\-delimited task that is encoded into the S3 Batch Operations manifest\. The task indicates which revision operation is applied to the specified object\.  

```
import logging
from urllib import parse
import boto3
from botocore.exceptions import ClientError

logger = logging.getLogger(__name__)
logger.setLevel('INFO')

s3 = boto3.resource('s3')


def lambda_handler(event, context):
    """
    Applies the specified revision to the specified object.

    :param event: The Amazon S3 batch event that contains the ID of the object to
                  revise and the revision type to apply.
    :param context: Context about the event.
    :return: A result structure that Amazon S3 uses to interpret the result of the
             operation.
    """
    # Parse job parameters from Amazon S3 batch operations
    invocation_id = event['invocationId']
    invocation_schema_version = event['invocationSchemaVersion']

    results = []
    result_code = None
    result_string = None

    task = event['tasks'][0]
    task_id = task['taskId']
    # The revision type is packed with the object key as a pipe-delimited string.
    obj_key, revision = \
        parse.unquote(task['s3Key'], encoding='utf-8').split('|')
    bucket_name = task['s3BucketArn'].split(':')[-1]

    logger.info("Got task: apply revision %s to %s.", revision, obj_key)

    try:
        stanza_obj = s3.Bucket(bucket_name).Object(obj_key)
        stanza = stanza_obj.get()['Body'].read().decode('utf-8')
        if revision == 'lower':
            stanza = stanza.lower()
        elif revision == 'upper':
            stanza = stanza.upper()
        elif revision == 'reverse':
            stanza = stanza[::-1]
        elif revision == 'delete':
            pass
        else:
            raise TypeError(f"Can't handle revision type '{revision}'.")

        if revision == 'delete':
            stanza_obj.delete()
            result_string = f"Deleted stanza {stanza_obj.key}."
        else:
            stanza_obj.put(Body=bytes(stanza, 'utf-8'))
            result_string = f"Applied revision type '{revision}' to " \
                            f"stanza {stanza_obj.key}."

        logger.info(result_string)
        result_code = 'Succeeded'
    except ClientError as error:
        if error.response['Error']['Code'] == 'NoSuchKey':
            result_code = 'Succeeded'
            result_string = f"Stanza {obj_key} not found, assuming it was deleted " \
                            f"in an earlier revision."
            logger.info(result_string)
        else:
            result_code = 'PermanentFailure'
            result_string = f"Got exception when applying revision type '{revision}' " \
                            f"to {obj_key}: {error}."
            logger.exception(result_string)
    finally:
        results.append({
            'taskId': task_id,
            'resultCode': result_code,
            'resultString': result_string
        })
    return {
        'invocationSchemaVersion': invocation_schema_version,
        'treatMissingKeysAs': 'PermanentFailure',
        'invocationId': invocation_id,
        'results': results
    }
```

## Learning from S3 Batch Operations tutorial<a name="batch-ops-tutorials-lambda"></a>

The following tutorial presents complete end\-to\-end procedures for some Batch Operations tasks with Lambda\.
+ [Tutorial: Batch\-transcoding videos with S3 Batch Operations, AWS Lambda, and AWS Elemental MediaConvert](tutorial-s3-batchops-lambda-mediaconvert-video.md)