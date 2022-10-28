# Using a bucket\-style alias for your S3 bucket access point<a name="access-points-alias"></a>

When you create an access point, Amazon S3 automatically generates an alias that you can use instead of an Amazon S3 bucket name for data access\. You can use this access point alias instead of an Amazon Resource Name \(ARN\) for access point data plane operations\. For a list of these operations, see [Access point compatibility with AWS services](access-points-usage-examples.md#access-points-service-api-support)\.

The following shows an example ARN and access point alias for an access point named `my-access-point`\.
+ **ARN** – `arn:aws:s3:region:account-id:accesspoint/my-access-point`
+ **Access point alias** – `my-access-point-hrzrlukc5m36ft7okagglf3gmwluquse1b-s3alias`

For more information about ARNs, see [Amazon Resource Names \(ARNs\)](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html) in the *AWS General Reference*\.

## Access point alias names<a name="access-points-alias-name"></a>

An access point alias name is created within the same namespace as an Amazon S3 bucket\. This alias name is automatically generated and cannot be changed\. An access point alias name meets all the requirements of a valid Amazon S3 bucket name and consists of the following parts:

`access point prefix-metadata-s3alias`

**Note**  
The `-s3alias` suffix is reserved for access point alias names and can't be used for bucket or access point names\. For more information about Amazon S3 bucket\-naming rules, see [Bucket naming rules](bucketnamingrules.md)\.

## Access point alias use cases and limitations<a name="access-points-alias-use-case"></a>

When adopting access points, you can use access point alias names without requiring extensive code changes\.

When you create an access point, Amazon S3 automatically generates an access point alias name, as shown in the following example\. To run this command, replace the `user input placeholders` with your own information\.

```
aws s3control create-access-point --bucket DOC-EXAMPLE-BUCKET1 --name my-access-point --account-id 111122223333
{
    "AccessPointArn":
    "arn:aws:s3:region:111122223333:accesspoint/my-access-point",
    "Alias": "my-access-point-aqfqprnstn7aefdfbarligizwgyfouse1a-s3alias"
}
```

You can use this access point alias name instead of an Amazon S3 bucket name in any data plane operation\. For a list of these operations, see [Access point compatibility with AWS services](access-points-usage-examples.md#access-points-service-api-support)\.

The following AWS CLI example for the `get-object` command uses the bucket's access point alias to return information about the specified object\. To run this command, replace the `user input placeholders` with your own information\.

```
aws s3api get-object --bucket my-access-point-aqfqprnstn7aefdfbarligizwgyfouse1a-s3alias --key dir/my_data.rtf my_data.rtf
            
{
    "AcceptRanges": "bytes",
    "LastModified": "2020-01-08T22:16:28+00:00",
    "ContentLength": 910,
    "ETag": "\"00751974dc146b76404bb7290f8f51bb\"",
    "VersionId": "null",
    "ContentType": "text/rtf",
    "Metadata": {}
}
```

**Limitations**
+ Aliases cannot be configured by customers\.
+ Aliases cannot be deleted or modified or disabled on an access point\.
+ You can use this access point alias name instead of an Amazon S3 bucket name in some data plane operations\. For a list of these operations, see [Access point compatibility with S3 operations](access-points-usage-examples.md#access-points-operations-support)\.
+ You can't use an access point alias name for Amazon S3 control plane operations\. For a list of Amazon S3 control plane operations, see [Amazon S3 Control](https://docs.aws.amazon.com/AmazonS3/latest/API/API_Operations_AWS_S3_Control.html) in the *Amazon Simple Storage Service API Reference*\.
+ Aliases cannot be used in AWS Identity and Access Management \(IAM\) policies\.
+ Aliases cannot be used as a logging destination for S3 server access logs\.
+ Aliases cannot be used as a logging destination for AWS CloudTrail logs\.
+ Amazon SageMaker GroundTruth does not support access point aliases\.