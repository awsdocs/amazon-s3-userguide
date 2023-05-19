# Configuring block public access settings for your S3 buckets<a name="configuring-block-public-access-bucket"></a>

Amazon S3 Block Public Access provides settings for access points, buckets, and accounts to help you manage public access to Amazon S3 resources\. By default, new buckets, access points, and objects do not allow public access\.

For more information, see [Blocking public access to your Amazon S3 storage](access-control-block-public-access.md)\.

You can use the S3 console, AWS CLI, AWS SDKs, and REST API to grant public access to one or more buckets\. You can also block public access to buckets that are already public\. For more information, see the sections below\.

To configure block public access settings for every bucket in your account, see [Configuring block public access settings for your account](configuring-block-public-access-account.md)\. For information about configuring block public access for access points, see [Performing block public access operations on an access point](access-control-block-public-access.md#access-control-block-public-access-examples-access-point)\.

## Using the AWS CLI<a name="configuring-block-public-access-bucket-cli"></a>

To block public access on a bucket or to delete the public access block, use the AWS CLI service `s3api`\. The bucket\-level operations that use this service are as follows:
+ PUT PublicAccessBlock \(for a bucket\)
+ GET PublicAccessBlock \(for a bucket\)
+ DELETE PublicAccessBlock \(for a bucket\)
+ GET BucketPolicyStatus

For more information and examples, see [put\-public\-access\-block](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/put-public-access-block.html) in the *AWS CLI Reference*\.

## Using the AWS SDKs<a name="configuring-block-public-access-bucket-sdk"></a>

------
#### [ Java ]

```
AmazonS3 client = AmazonS3ClientBuilder.standard()
	  .withCredentials(<credentials>)
	  .build();

client.setPublicAccessBlock(new SetPublicAccessBlockRequest()
		.withBucketName(<bucket-name>)
		.withPublicAccessBlockConfiguration(new PublicAccessBlockConfiguration()
				.withBlockPublicAcls(<value>)
				.withIgnorePublicAcls(<value>)
				.withBlockPublicPolicy(<value>)
				.withRestrictPublicBuckets(<value>)));
```

**Important**  
This example pertains only to bucket\-level operations, which use the `AmazonS3` client class\. For account\-level operations, see the following example\.

------
#### [ Other SDKs ]

For information about using the other AWS SDKs, see [Developing with Amazon S3 using the AWS SDKs, and explorers](UsingAWSSDK.md)\.

------

## Using the REST API<a name="configuring-block-public-access-bucket-api"></a>

For information about using Amazon S3 Block Public Access through the REST APIs, see the following topics in the *Amazon Simple Storage Service API Reference*\.
+ Bucket\-level operations
  + [PUT PublicAccessBlock](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTPublicAccessBlock.html)
  + [GET PublicAccessBlock](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETPublicAccessBlock.html)
  + [DELETE PublicAccessBlock](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketDELETEPublicAccessBlock.html)
  + [GET BucketPolicyStatus](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETPolicyStatus.html)