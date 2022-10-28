# Using a bucket\-style alias for your S3 on Outposts bucket access point<a name="s3-outposts-access-points-alias"></a>

With S3 on Outposts, you must use access points to access any object in an Outposts bucket\. Every time you create an access point for a bucket, S3 on Outposts automatically generates an access point alias\. You can use this access point alias instead of an access point ARN for any data plane operation\. For example, you can use an access point alias to perform object\-level operations such as PUT, GET, LIST, and more\. For a list of these operations, see [Amazon S3 API operations for managing objects](S3OutpostsAPI.md#S3OutpostsAPIsObject)\.

The following examples show an ARN and access point alias for an access point named `my-access-point`\.
+ **Access point ARN** – `arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/accesspoint/my-access-point`
+ **Access point alias** – `my-access-po-o01ac5d28a6a232904e8xz5w8ijx1qzlbp3i3kuse10--op-s3`

For more information about ARNs, see [Amazon Resource Names \(ARNs\)](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html) in the *AWS General Reference*\.

For more information about access point aliases, see the following topics\.

**Topics**
+ [Access point aliases](#access-points-alias-name-s3-outposts)
+ [Using an access point alias in an S3 on Outposts object operation](#access-points-alias-use-case-s3-outposts)
+ [Limitations](#access-points-alias-limitations-s3-outposts)

## Access point aliases<a name="access-points-alias-name-s3-outposts"></a>

An access point alias is created within the same namespace as an S3 on Outposts bucket\. When you create an access point, S3 on Outposts automatically generates an access point alias that cannot be changed\. An access point alias meets all the requirements of a valid S3 on Outposts bucket name and consists of the following parts:

`access point name prefix-metadata--op-s3`

**Note**  
The `--op-s3` suffix is reserved for access point aliases, we recommend that you don't use it for bucket or access point names\. For more information about S3 on Outposts bucket\-naming rules, see [Working with S3 on Outposts buckets](S3OutpostsWorkingBuckets.md)\.

### Finding the access point alias<a name="access-points-alias-name-s3-outposts-find"></a>

The following examples show you how to find an access point alias by using the Amazon S3 console and the AWS CLI\.

**Example : Find and copy an access point alias in the Amazon S3 console**  
After you create an access point in the console, you can get the access point alias from the **Access Point alias** column in the **Access Points** list\.   

**To copy an access point alias**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Outposts access points**\.

1. To copy the access point alias, do one of the following:
   + In the **Access Points** list, select the option button next to the access point name, and then choose **Copy Access Point alias**\.
   + Choose the access point name\. Then, under **Outposts access point overview**, copy the access point alias\.

**Example : Create an access point by using the AWS CLI and find the access point alias in the response**  
The following AWS CLI example for the `create-access-point` command creates the access point and returns the automatically generated access point alias\. To run this command, replace the `user input placeholders` with your own information\.  

```
aws s3control create-access-point --bucket example-outposts-bucket --name example-outposts-access-point --account-id 123456789012

{
    "AccessPointArn":
    "arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/accesspoint/example-outposts-access-point",
    "Alias": "example-outp-o01ac5d28a6a232904e8xz5w8ijx1qzlbp3i3kuse10--op-s3"
}
```

**Example : Get an access point alias by using the AWS CLI**  
The following AWS CLI example for the `get-access-point` command returns information about the specified access point\. This information includes the access point alias\. To run this command, replace the `user input placeholders` with your own information\.  

```
aws s3control get-access-point --bucket arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/bucket/example-outposts-bucket --name example-outposts-access-point --account-id 123456789012

{
    "Name": "example-outposts-access-point",
    "Bucket": "example-outposts-bucket",
    "NetworkOrigin": "Vpc",
    "VpcConfiguration": {
        "VpcId": "vpc-01234567890abcdef"
    },
    "PublicAccessBlockConfiguration": {
        "BlockPublicAcls": true,
        "IgnorePublicAcls": true,
        "BlockPublicPolicy": true,
        "RestrictPublicBuckets": true
    },
    "CreationDate": "2022-09-18T17:49:15.584000+00:00",
    "Alias": "example-outp-o0b1d075431d83bebde8xz5w8ijx1qzlbp3i3kuse10--op-s3"
}
```

**Example : List access points to find an access point alias by using the AWS CLI**  
The following AWS CLI example for the `list-access-points` command lists information about the specified access point\. This information includes the access point alias\. To run this command, replace the `user input placeholders` with your own information\.  

```
aws s3control list-access-points --account-id 123456789012 --bucket arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/bucket/example-outposts-bucket

{
    "AccessPointList": [
        {
            "Name": "example-outposts-access-point",
            "NetworkOrigin": "Vpc",
            "VpcConfiguration": {
                "VpcId": "vpc-01234567890abcdef"
            },
            "Bucket": "example-outposts-bucket",
            "AccessPointArn": "arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/accesspoint/example-outposts-access-point",
            "Alias": "example-outp-o0b1d075431d83bebde8xz5w8ijx1qzlbp3i3kuse10--op-s3"
        }
    ]
}
```

## Using an access point alias in an S3 on Outposts object operation<a name="access-points-alias-use-case-s3-outposts"></a>

When adopting access points, you can use access point alias without requiring extensive code changes\.

This AWS CLI example shows a `get-object` operation for an S3 on Outposts bucket\. This example uses the access point alias as the value for `--bucket` instead of the full access point ARN\.

```
aws s3api get-object --bucket my-access-po-o0b1d075431d83bebde8xz5w8ijx1qzlbp3i3kuse10--op-s3 --key  testkey  sample-object.rtf

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

## Limitations<a name="access-points-alias-limitations-s3-outposts"></a>
+ Aliases cannot be configured by customers\.
+ Aliases cannot be deleted or modified or disabled on an access point\.
+ You can't use an access point alias for S3 on Outposts control plane operations\. For a list of S3 on Outposts control plane operations, see [Amazon S3 Control API operations for managing buckets](S3OutpostsAPI.md#S3OutpostsAPIsBucket)\.
+ Aliases cannot be used in AWS Identity and Access Management \(IAM\) policies\.