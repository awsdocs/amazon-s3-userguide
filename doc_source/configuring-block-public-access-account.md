# Configuring block public access settings for your account<a name="configuring-block-public-access-account"></a>

Amazon S3 Block Public Access provides settings for access points, buckets, and accounts to help you manage public access to Amazon S3 resources\. By default, new buckets, access points, and objects do not allow public access\.

For more information, see [Blocking public access to your Amazon S3 storage](access-control-block-public-access.md)\.

You can use the S3 console, AWS CLI, AWS SDKs, and REST API to configure block public access settings for all the buckets in your account\. For more information, see the sections below\.

To configure block public access settings for your buckets, see [Configuring block public access settings for your S3 buckets](configuring-block-public-access-bucket.md)\. For information about access points, see [Performing block public access operations on an access point](access-control-block-public-access.md#access-control-block-public-access-examples-access-point)\.

## Using the S3 console<a name="block-public-access-account"></a>

Amazon S3 block public access prevents the application of any settings that allow public access to data within S3 buckets\. This section describes how to edit block public access settings for all the S3 buckets in your AWS account\. For more information about blocking public access, see [Blocking public access to your Amazon S3 storage](access-control-block-public-access.md)\.

**To edit block public access settings for all the S3 buckets in an AWS account**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose **Block Public Access settings for this account**\.

1. Choose **Edit** to change the block public access settings for all the buckets in your AWS account\.

1. Choose the settings that you want to change, and then choose **Save changes**\.

1. When you're asked for confirmation, enter **confirm**\. Then choose **Confirm** to save your changes\.

## Using the AWS CLI<a name="access-control-block-public-access-examples-cli"></a>

You can use Amazon S3 Block Public Access through the AWS CLI\. For more information about setting up and using the AWS CLI, see [What is the AWS Command Line Interface?](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html) 

**Account**
+ To perform block public access operations on an account, use the AWS CLI service `s3control`\. The account\-level operations that use this service are as follows:
  + PUT PublicAccessBlock \(for an account\)
  + GET PublicAccessBlock \(for an account\)
  + DELETE PublicAccessBlock \(for an account\)

For additional information and examples, see [put\-public\-access\-block](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3control/put-public-access-block.html) in the *AWS CLI Reference*\.

## Using the AWS SDKs<a name="access-control-block-public-access-examples-sdk"></a>

------
#### [ Java ]

The following examples show you how to use Amazon S3 Block Public Access with the AWS SDK for Java to put a public access block configuration on an Amazon S3 account\. For instructions on how to create and test a working sample, see [Using the AWS SDK for Java](UsingTheMPJavaAPI.md)\.

```
AWSS3ControlClientBuilder controlClientBuilder = AWSS3ControlClientBuilder.standard();
controlClientBuilder.setRegion(<region>);
controlClientBuilder.setCredentials(<credentials>);
					
AWSS3Control client = controlClientBuilder.build();
client.putPublicAccessBlock(new PutPublicAccessBlockRequest()
		.withAccountId(<account-id>)
		.withPublicAccessBlockConfiguration(new PublicAccessBlockConfiguration()
				.withIgnorePublicAcls(<value>)
				.withBlockPublicAcls(<value>)
				.withBlockPublicPolicy(<value>)
				.withRestrictPublicBuckets(<value>)));
```

**Important**  
This example pertains only to account\-level operations, which use the `AWSS3Control` client class\. For bucket\-level operations, see the preceding example\.

------
#### [ Other SDKs ]

For information about using the other AWS SDKs, see [Developing with Amazon S3 using the AWS SDKs, and explorers](UsingAWSSDK.md)\.

------

## Using the REST API<a name="access-control-block-public-access-examples-api"></a>

For information about using Amazon S3 Block Public Access through the REST APIs, see the following topics in the *Amazon Simple Storage Service API Reference*\.
+ Account\-level operations
  + [PUT PublicAccessBlock](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTAccountPUTPublicAccessBlock.html)
  + [GET PublicAccessBlock](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTAccountGETPublicAccessBlock.html)
  + [DELETE PublicAccessBlock](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTAccountDELETEPublicAccessBlock.html)
