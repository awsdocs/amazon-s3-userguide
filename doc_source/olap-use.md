# Using Amazon S3 Object Lambda Access Points<a name="olap-use"></a>

Making requests through S3 Object Lambda access points works the same as making requests through other access points\. For more information about how to make requests through an access point, see [Using access points](using-access-points.md)\. You can make requests through Object Lambda access points by using the AWS Management Console, AWS Command Line Interface \(AWS CLI\), AWS SDKs, or Amazon S3 REST API\.

**Important**  
The Amazon Resource Names \(ARNs\) for Object Lambda access points use a service name of `s3-object-lambda`\. Thus, Object Lambda access point ARNs begin with `arn:aws::s3-object-lambda`, instead of `arn:aws::s3`, which is used with other access points\.

## How to find the ARN for your Object Lambda access point<a name="olap-find-arn"></a>

To use an Object Lambda access point with the AWS CLI or AWS SDKs, you need to know the Amazon Resource Name \(ARN\) of the Object Lambda access point\. The following examples show how to find the ARN for an Object Lambda access point by using the AWS Management Console or AWS CLI\. 

------
#### [ AWS Management Console ]

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Object Lambda Access Points**\.

1. Select the option button next to the Object Lambda access point whose ARN you want to copy\.

1. Choose **Copy ARN**\.

------
#### [ AWS CLI ]

1. To retrieve a list of the Object Lambda access points that are associated with your AWS account, run the following command\. Before running the command, replace the account ID *`111122223333`* with your AWS account ID\.

   ```
   aws s3control list-access-points-for-object-lambda --account-id 111122223333
   ```

1. Review the command output to find the Object Lambda access point ARN that you want to use\. The output of the previous command should look similar to the following example\.

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

------

## S3 Object Lambda tutorials<a name="olap-tutorials-use"></a>

The following tutorials present complete end\-to\-end procedures for some S3 Object Lambda tasks\.
+ [Tutorial: Transforming data for your application with S3 Object Lambda](tutorial-s3-object-lambda-uppercase.md)
+ [Tutorial: Detecting and redacting PII data with S3 Object Lambda and Amazon Comprehend](tutorial-s3-object-lambda-redact-pii.md)