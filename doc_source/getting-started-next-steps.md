# Next steps<a name="getting-started-next-steps"></a>

In the preceding examples, you learned how to perform some basic Amazon S3 tasks\.

The following topics explain the learning paths that you can use to gain a deeper understanding of Amazon S3 so that you can implement it in your applications\.

**Topics**
+ [Understand common use cases](#s3-use-cases)
+ [Control access to your buckets and objects](#control-access-resources)
+ [Explore training and support](#explore-training-and-support)
+ [Manage and monitor your storage](#manage-monitor-storage)
+ [Develop with Amazon S3](#develop-with-s3)

## Understand common use cases<a name="s3-use-cases"></a>

You can use Amazon S3 to support your specific use case\. The [AWS Solutions Library](http://aws.amazon.com/solutions/) and [AWS Blog](http://aws.amazon.com/blogs/) provide use\-case specific information and tutorials\. The following are some common use cases for Amazon S3:
+ **Backup and storage** – Use Amazon S3 storage management features to manage costs, meet regulatory requirements, reduce latency, and save multiple distinct copies of your data for compliance requirements\.
+ **Application hosting** – Deploy, install, and manage web applications that are reliable, highly scalable, and low\-cost\. For example, you can configure your Amazon S3 bucket to host a static website\. For more information, see [Hosting a static website using Amazon S3](WebsiteHosting.md)\.
+ **Media hosting** – Build a highly available infrastructure that hosts video, photo, or music uploads and downloads\.
+ **Software delivery** – Host your software applications for customers to download\.

## Control access to your buckets and objects<a name="control-access-resources"></a>

Amazon S3 provides a variety of security features and tools\. For an overview, see [Access control best practices](access-control-best-practices.md)\.

By default, S3 buckets and the objects in them are private\. You have access only to the S3 resources that you create\. You can use the following features to grant granular resource permissions that support your specific use case or to audit the permissions of your Amazon S3 resources\. 
+ [S3 Block Public Access](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-control-block-public-access.html) – Block public access to S3 buckets and objects\. By default, Block Public Access settings are turned on at the account and bucket level\.
+ [AWS Identity and Access Management \(IAM\)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-access-control.html) – Create IAM users for your AWS account to manage access to your Amazon S3 resources\. For example, you can use IAM with Amazon S3 to control the type of access a user or group of users has to an Amazon S3 bucket that your AWS account owns\.
+ [Bucket policies](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucket-policies.html) – Use IAM\-based policy language to configure resource\-based permissions for your S3 buckets and the objects in them\.
+ [Access control lists \(ACLs\)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/acls.html) – Grant read and write permissions for individual buckets and objects to authorized users\. As a general rule, we recommend using S3 resource\-based policies \(bucket policies and access point policies\) or IAM policies for access control instead of ACLs\. ACLs are an access control mechanism that predates resource\-based policies and IAM\. For more information about when you'd use ACLs instead of resource\-based policies or IAM policies, see [Access policy guidelines](access-policy-alternatives-guidelines.md)\.
+ [S3 Object Ownership](https://docs.aws.amazon.com/AmazonS3/latest/userguide/about-object-ownership.html) – Disable ACLs and take ownership of every object in your bucket, simplifying access management for data stored in Amazon S3\. You, as the bucket owner, automatically own and have full control over every object in your bucket, and access control for your data is based on policies\.
+ [Access Analyzer for S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-analyzer.html) – Evaluate and monitor your S3 bucket access policies, ensuring that the policies provide only the intended access to your S3 resources\. 

## Explore training and support<a name="explore-training-and-support"></a>

You can learn from AWS experts to advance your skills and get expert assistance achieving your objectives\.
+ **Training** – Training resources provide a hands\-on approach to learning Amazon S3\. For more information, see [AWS training and certification](https://www.aws.training) and [AWS online tech talks](http://aws.amazon.com/events/online-tech-talks)\.
+ **Discussion Forums** – On the forum, you can review posts to understand what you can and can't do with Amazon S3\. You can also post your questions\. For more information, see [Discussion Forums](http://forums.aws.amazon.com/index.jspa)\.
+ **Technical Support** – If you have further questions, you can contact [Technical Support](http://aws.amazon.com/contact-us)\.

## Manage and monitor your storage<a name="manage-monitor-storage"></a>
+ [Managing your storage](managing-storage.md) – After you create buckets and upload objects in Amazon S3, you can manage your object storage\. For example, you can use S3 Versioning and S3 Replication for disaster recovery, S3 Lifecycle to manage storage costs, and S3 Object Lock to meet compliance requirements\.
+ [Monitoring your storage](monitoring-overview.md) – Monitoring is an important part of maintaining the reliability, availability, and performance of Amazon S3 and your AWS solutions\. You can monitor storage activity and costs\. Also, we recommend that you collect monitoring data from all the parts of your AWS solution so that you can more easily debug a multipoint failure if one occurs\. 
+ [Analytics and insights](analytics-insights.md) – You can also use analytics and insights in Amazon S3 to understand, analyze, and optimize your storage usage\. For example, use [Amazon S3 Storage Lens](storage_lens.md) to understand, analyze, and optimize your storage\. S3 Storage Lens provides 29\+ usage and activity metrics and interactive dashboards to aggregate data for your entire organization, specific accounts, Regions, buckets, or prefixes\. Use [Storage Class Analysis](analytics-storage-class.md) to analyze storage access patterns to decide when it's time to move your data to a more cost\-effective storage class\. 

## Develop with Amazon S3<a name="develop-with-s3"></a>

Amazon S3 is a REST service\. You can send requests to Amazon S3 using the REST API or the AWS SDK libraries, which wrap the underlying Amazon S3 REST API, simplifying your programming tasks\. You can also use the AWS Command Line Interface \(AWS CLI\) to make Amazon S3 API calls\. For more information, see [Making requests](MakingRequests.md)\.

The Amazon S3 REST API is an HTTP interface to Amazon S3\. With the REST API, you use standard HTTP requests to create, fetch, and delete buckets and objects\. To use the REST API, you can use any toolkit that supports HTTP\. You can even use a browser to fetch objects, as long as they are anonymously readable\. For more information, see [Developing with Amazon S3 using the REST API](developing-rest-api.md)\.

To help you build applications using the language of your choice, we provide the following resources\.

**AWS CLI**  
You can access the features of Amazon S3 using the AWS CLI\. To download and configure the AWS CLI, see [Developing with Amazon S3 using the AWS CLI](setup-aws-cli.md)\.

The AWS CLI provides two tiers of commands for accessing Amazon S3: High\-level \([s3](https://docs.aws.amazon.com/cli/latest/userguide/cli-services-s3-commands.html)\) commands and API\-level \([s3api](https://docs.aws.amazon.com/cli/latest/userguide/cli-services-s3-apicommands.html) and [s3control](https://docs.aws.amazon.com/AmazonS3/latest/userguide/setup-aws-cli.html)\) commands\. The high\-level S3 commands simplify performing common tasks, such as creating, manipulating, and deleting objects and buckets\. The s3api and s3control commands expose direct access to all Amazon S3 API operations, which you can use to carry out advanced operations that might not be possible with the high\-level commands alone\.

For a list of Amazon S3 AWS CLI commands, see [s3](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3/index.html), [s3api](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/index.html), and [s3control](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3control/index.html)\.

**AWS SDKs and Explorers**  
You can use the AWS SDKs when developing applications with Amazon S3\. The AWS SDKs simplify your programming tasks by wrapping the underlying REST API\. The AWS Mobile SDKs and the Amplify JavaScript library are also available for building connected mobile and web applications using AWS\.

In addition to the AWS SDKs, AWS Explorers are available for Visual Studio and Eclipse for Java IDE\. In this case, the SDKs and the explorers are bundled together as AWS Toolkits\.

For more information, see [Developing with Amazon S3 using the AWS SDKs, and explorers](UsingAWSSDK.md)\.

**Sample Code and Libraries**  
The [AWS Developer Center](http://aws.amazon.com/code/Amazon-S3) and [AWS Code Sample Catalog](https://docs.aws.amazon.com/code-samples/latest/catalog/welcome.html) have sample code and libraries written especially for Amazon S3\. You can use these code samples to understand how to implement the Amazon S3 API\. You can also view the [https://docs.aws.amazon.com/AmazonS3/latest/API/Welcome.html](https://docs.aws.amazon.com/AmazonS3/latest/API/Welcome.html) to understand the Amazon S3 API operations in detail\.