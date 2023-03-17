# Using Amazon S3 Object Lambda Access Points<a name="olap-use"></a>

Making requests through Amazon S3 Object Lambda Access Points works the same as making requests through other access points\. For more information about how to make requests through an access point, see [Using access points](using-access-points.md)\. You can make requests through Object Lambda Access Points by using the Amazon S3 console, AWS Command Line Interface \(AWS CLI\), AWS SDKs, or Amazon S3 REST API\.

**Important**  
The Amazon Resource Names \(ARNs\) for Object Lambda Access Points use a service name of `s3-object-lambda`\. Thus, Object Lambda Access Point ARNs begin with `arn:aws::s3-object-lambda`, instead of `arn:aws::s3`, which is used with other access points\.

## How to find the ARN for your Object Lambda Access Point<a name="olap-find-arn"></a>

To use an Object Lambda Access Point with the AWS CLI or AWS SDKs, you need to know the Amazon Resource Name \(ARN\) of the Object Lambda Access Point\. The following examples show how to find the ARN for an Object Lambda Access Point by using the Amazon S3 console or AWS CLI\. 

### Using the S3 console<a name="olap-use-arn-console"></a>

**To find the ARN for your Object Lambda Access Point by using the console**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Object Lambda Access Points**\.

1. Choose the option button next to the Object Lambda Access Point whose ARN you want to copy\.

1. Choose **Copy ARN**\.

### Using the AWS CLI<a name="olap-use-arn-cli"></a>

**To find the ARN for your Object Lambda Access Point by using the AWS CLI**

1. To retrieve a list of the Object Lambda Access Points that are associated with your AWS account, run the following command\. Before running the command, replace the account ID *`111122223333`* with your AWS account ID\.

   ```
   aws s3control list-access-points-for-object-lambda --account-id 111122223333
   ```

1. Review the command output to find the Object Lambda Access Point ARN that you want to use\. The output of the previous command should look similar to the following example\.

   ```
   {
       "ObjectLambdaAccessPointList": [
           {
               "Name": "my-object-lambda-ap",
               "ObjectLambdaAccessPointArn": "arn:aws:s3-object-lambda:us-east-1:111122223333:accesspoint/my-object-lambda-ap"
           },
           ...
       ]
   }
   ```

## How to use a bucket\-style alias for your S3 bucket Object Lambda Access Point<a name="ol-access-points-alias"></a>

When you create an Object Lambda Access Point, Amazon S3 automatically generates a unique alias for your Object Lambda Access Point\. You can use this alias instead of an Amazon S3 bucket name or the Object Lambda Access Point Amazon Resource Name \(ARN\) in a request for access point data plane operations\. For a list of these operations, see [Access point compatibility with AWS services](access-points-usage-examples.md#access-points-service-api-support)\.

An Object Lambda Access Point alias name is created within the same namespace as an Amazon S3 bucket\. This alias name is automatically generated and cannot be changed\. For an existing Object Lambda Access Point, an alias is automatically assigned for use\. An Object Lambda Access Point alias name meets all the requirements of a valid Amazon S3 bucket name and consists of the following parts:

`Object Lambda Access Point name prefix-metadata--ol-s3`

**Note**  
The `--ol-s3` suffix is reserved for Object Lambda Access Point alias names and can't be used for bucket or Object Lambda Access Point names\. For more information about Amazon S3 bucket\-naming rules, see [Bucket naming rules](bucketnamingrules.md)\.

The following examples show the ARN and the Object Lambda Access Point alias for an Object Lambda Access Point named `my-object-lambda-access-point`:
+ **ARN** – `arn:aws:s3-object-lambda:region:account-id:accesspoint/my-object-lambda-access-point`
+ **Object Lambda Access Point alias** – `my-object-lambda-acc-1a4n8yjrb3kda96f67zwrwiiuse1a--ol-s3`

When you use an Object Lambda Access Point, you can use the Object Lambda Access Point alias name without requiring extensive code changes\.

When you delete an Object Lambda Access Point, the Object Lambda Access Point alias name becomes inactive and unprovisioned\.

### How to find the alias for your Object Lambda Access Point<a name="olap-find-alias"></a>

#### Using the S3 console<a name="olap-use-alias-console"></a>

**To find the alias for your Object Lambda Access Point by using the console**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Object Lambda Access Points**\.

1. For the Object Lambda Access Point that you want to use, copy the **Object Lambda Access Point alias** value\.

#### Using the AWS CLI<a name="olap-use-alias-cli"></a>

When you create an Object Lambda Access Point, Amazon S3 automatically generates an Object Lambda Access Point alias name, as shown in the following example command\. To run this command, replace the `user input placeholders` with your own information\. For information about how to create an Object Lambda Access Point by using the AWS CLI, see [To create an Object Lambda Access Point by using the AWS CLI](olap-create.md#olap-create-cli-specific)\.

```
aws s3control create-access-point-for-object-lambda --account-id 111122223333 --name my-object-lambda-access-point --configuration file://my-olap-configuration.json
{
    "ObjectLambdaAccessPointArn": "arn:aws:s3:region:111122223333:accesspoint/my-access-point",
    "Alias": {
        "Value": "my-object-lambda-acc-1a4n8yjrb3kda96f67zwrwiiuse1a--ol-s3",
        "Status": "READY"
    }
}
```

The generated Object Lambda Access Point alias name has two fields: 
+ The `Value` field is the alias value of the Object Lambda Access Point\. 
+ The `Status` field is the status of the Object Lambda Access Point alias\. If the status is `PROVISIONING`, Amazon S3 is provisioning the Object Lambda Access Point alias, and the alias is not yet ready for use\. If the status is `READY`, the Object Lambda Access Point alias has been successfully provisioned and is ready for use\.

For more information about the `ObjectLambdaAccessPointAlias` data type in the REST API, see [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_CreateAccessPointForObjectLambda.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_CreateAccessPointForObjectLambda.html) and [https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_ObjectLambdaAccessPointAlias.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_ObjectLambdaAccessPointAlias.html) in the *Amazon Simple Storage Service API Reference*\.

### How to use the Object Lambda Access Point alias<a name="use-olap-alias"></a>

You can use an Object Lambda Access Point alias instead of an Amazon S3 bucket name for the operations listed in [Access point compatibility with AWS services](access-points-usage-examples.md#access-points-service-api-support)\.

The following AWS CLI example for the `get-bucket-location` command uses the bucket's access point alias to return the AWS Region that the bucket is in\. To run this command, replace the `user input placeholders` with your own information\.

```
aws s3api get-bucket-location --bucket my-object-lambda-acc-w7i37nq6xuzgax3jw3oqtifiusw2a--ol-s3
            
{
    "LocationConstraint": "us-west-2"
}
```

If the Object Lambda Access Point alias in a request isn't valid, the error code `InvalidAccessPointAliasError` is returned\. For more information about `InvalidAccessPointAliasError`, see [List of Error Codes](https://docs.aws.amazon.com/AmazonS3/latest/API/ErrorResponses.html#ErrorCodeList) in the *Amazon Simple Storage Service API Reference*\.

The limitations of an Object Lambda Access Point alias are the same as those of an access point alias\. For more information about the limitations of an access point alias, see [Limitations](access-points-alias.md#use-ap-alias-limitations)\.