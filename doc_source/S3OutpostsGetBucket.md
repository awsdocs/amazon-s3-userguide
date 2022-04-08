# Getting an S3 on Outposts bucket using the AWS CLI and the SDK for Java<a name="S3OutpostsGetBucket"></a>

With Amazon S3 on Outposts, you can create S3 buckets on your AWS Outposts and easily store and retrieve objects on premises for applications that require local data access, local data processing, and data residency\. S3 on Outposts provides a new storage class, S3 Outposts \(`OUTPOSTS`\), which uses the Amazon S3 APIs, and is designed to store data durably and redundantly across multiple devices and servers on your AWS Outposts\. You communicate with your Outpost bucket by using an access point and endpoint connection over a virtual private cloud \(VPC\)\. You can use the same APIs and features on Outpost buckets as you do on Amazon S3 buckets, including access policies, encryption, and tagging\. You can use S3 on Outposts through the AWS Management Console, AWS Command Line Interface \(AWS CLI\), AWS SDKs, or REST API\. For more information, see [What is Amazon S3 on Outposts?](S3onOutposts.md)

The following examples show you how to get an S3 on Outposts bucket using the AWS Command Line Interface \(AWS CLI\) and AWS SDK for Java\.

## Using the AWS CLI<a name="S3OutpostsGetBucketCLI"></a>

The following S3 on Outposts example gets a bucket using the AWS CLI\. 

```
aws s3control get-bucket --account-id 123456789012 --bucket "arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/bucket/example-outpost-bucket"
```

## Using the AWS SDK for Java<a name="S3OutpostsGetBucketJava"></a>

The following S3 on Outposts example gets a bucket using the SDK for Java\. 

```
import com.amazonaws.services.s3control.model.*;

public void getBucket(String bucketArn) {

    GetBucketRequest reqGetBucket = new GetBucketRequest()
            .withBucket(bucketArn)
            .withAccountId(AccountId);

    GetBucketResult respGetBucket = s3ControlClient.getBucket(reqGetBucket);
    System.out.printf("GetBucket Response: %s%n", respGetBucket.toString());

}
```