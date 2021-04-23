# Considerations going forward<a name="s3-gsg-ConsiderationsGoingForward"></a>

This section introduces you to topics you should consider before launching your own Amazon S3 product\.

**Topics**
+ [AWS account and security credentials](#iam-about-shared)
+ [Security](#s3-gsg-Security)
+ [AWS integration](#s3-gsg-AWSIntegration)
+ [Pricing](#s3-gsg-Pricing)

## AWS account and security credentials<a name="iam-about-shared"></a>

When you signed up for the service, you created an AWS account using an email address and password\. Those are your AWS account root user credentials\. As a best practice, you should not use your root user credentials to access AWS\. Nor should you give your credentials to anyone else\. Instead, create individual users for those who need access to your AWS account\. First, create an AWS Identity and Access Management \(IAM\) administrator user for yourself and use it for your daily work\. For details, see [Creating your first IAM admin user and group](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html) in the *IAM User Guide*\. Then create additional IAM users for other people\. For details, see [Creating your first IAM delegated user and group](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-delegated-user.html) in the *IAM User Guide*\.

If you're an account owner or administrator and want to know more about IAM, see the product description at [https://aws\.amazon\.com/iam](https://aws.amazon.com/iam) or the technical documentation in the [IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)\.

## Security<a name="s3-gsg-Security"></a>

Amazon S3 provides authentication mechanisms to secure data stored in Amazon S3 against unauthorized access\. Unless you specify otherwise, only the AWS account owner can access data uploaded to Amazon S3\. For more information about how to manage access to buckets and objects, go to [Identity and access management in Amazon S3](s3-access-control.md)\. 

You can also encrypt your data before uploading it to Amazon S3\.

## AWS integration<a name="s3-gsg-AWSIntegration"></a>

You can use Amazon S3 alone or in concert with one or more other Amazon products\. The following are the most common products used with Amazon S3:
+ [Amazon EC2](https://aws.amazon.com/ec2/)
+ [Amazon EMR](https://aws.amazon.com/elasticmapreduce/)
+ [Amazon SQS](https://aws.amazon.com/sqs/)
+ [Amazon CloudFront ](https://aws.amazon.com/cloudfront/)

## Pricing<a name="s3-gsg-Pricing"></a>

Learn the pricing structure for storing and transferring data on Amazon S3\. For more information, see [Amazon S3 pricing](https://aws.amazon.com/s3/pricing/)\.