# What is Amazon S3 on Outposts?<a name="S3onOutposts"></a>

AWS Outposts is a fully managed service that offers the same AWS infrastructure, AWS services, APIs, and tools to virtually any data center, co\-location space, or on\-premises facility for a truly consistent hybrid experience\. AWS Outposts is ideal for workloads that require low\-latency access to on\-premises systems, local data processing, data residency, and migration of applications with local system interdependencies\. For more information, see [What is AWS Outposts?](https://docs.aws.amazon.com/outposts/latest/userguide/what-is-outposts.htm) in the *AWS Outposts User Guide*\.

With Amazon S3 on Outposts, you can create S3 buckets on your Outposts and easily store and retrieve objects on premises\. S3 on Outposts provides a new storage class, `OUTPOSTS`, which uses the Amazon S3 APIs and is designed to store data durably and redundantly across multiple devices and servers on your Outposts\. You communicate with your Outposts bucket by using an access point and endpoint connection over a virtual private cloud \(VPC\)\. 

You can use the same APIs and features on Outposts buckets as you do on Amazon S3, including access policies, encryption, and tagging\. You can use S3 on Outposts through the AWS Management Console, AWS Command Line Interface \(AWS CLI\), AWS SDKs, or REST API\.
+ [How S3 on Outposts works](#S3OutpostsConcepts)
+ [Features of S3 on Outposts](#S3OutpostsFeatures)
+ [Related services ](#S3OutpostsRelatedAmazonWebServices)
+ [Accessing S3 on Outposts](#accessing-s3-outposts)
+ [Paying for S3 on Outposts](#S3OutpostsPayingforStorage)
+ [Next steps](#S3OutpostsNextSteps)

## How S3 on Outposts works<a name="S3OutpostsConcepts"></a>

S3 on Outposts is an object storage service that stores data as objects within buckets on your Outpost\. An *object* is a data file and any metadata that describes the file\. A *bucket* is a container for objects\. 

To store your data in S3 on Outposts, you first create a bucket\. When you create the bucket, you specify a bucket name and the Outpost that will hold the bucket\. To access your S3 on Outposts bucket and perform object operations, you next create and configure an access point\. You must also create an endpoint to route requests to your access point\. 

Access points simplify data access for any AWS service or customer application that stores data in S3\. Access points are named network endpoints that are attached to buckets and can be used to perform object operations, such as `GetObject` and `PutObject`\. Each access point has distinct permissions and network controls\. 

You can create and manage your S3 on Outposts buckets, access points, and endpoints by using the AWS Management Console, AWS CLI, AWS SDKs, or REST API\. To upload and manage objects in your S3 on Outposts bucket, you can use the AWS CLI, AWS SDKs, or REST API\. 

### Regions<a name="aws-region-s3-outposts"></a>

During AWS Outposts provisioning, you or AWS creates a service link connection that connects your Outpost back to your chosen AWS Region or Outposts home Region for bucket operations and telemetry\. An Outpost relies on connectivity to the parent AWS Region\. The Outposts rack is not designed for disconnected operations or environments with limited to no connectivity\. For more information, see [Outpost connectivity to AWS Regions](https://docs.aws.amazon.com/outposts/latest/userguide/how-outposts-works.html#region-connectivity) in the *AWS Outposts User Guide*\.

### Buckets<a name="BasicsBucketS3Outposts"></a>

A bucket is a container for objects stored in S3 on Outposts\. You can store any number of objects in a bucket and can have up to 100 buckets per account per Outpost\. 

When you create a bucket, you enter a bucket name and choose the Outpost where the bucket will reside\. After you create a bucket, you cannot change the bucket name or move the bucket to a different Outpost\. Bucket names must follow [Amazon S3 bucket naming rules](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucketnamingrules.html)\. In S3 on Outposts, bucket names are unique to an Outpost and AWS account\. S3 on Outposts buckets require the `outpost-id`, `account-id`, and bucket name to identify them\. 

The following example shows the Amazon Resource Name \(ARN\) format for S3 on Outposts buckets\. The ARN is comprised of the Region your Outpost is homed to, your Outpost account, the Outpost ID, and the bucket name\.

```
arn:aws:s3-outposts:region:account-id:outpost/outpost-id/bucket/bucket-name
```

Every object is contained in a bucket\. You must use access points to access any object in an Outposts bucket\. When you specify the bucket for object operations, you use the access point ARN or access point alias\. For more information about access point aliases, see [Using a bucket\-style alias for your S3 on Outposts bucket access point](s3-outposts-access-points-alias.md)\. 

The following example shows the access point ARN format for S3 on Outposts, which includes the `outpost-id`, `account-id`, and access point name:

```
arn:aws:s3-outposts:region:account-id:outpost/outpost-id/accesspoint/accesspoint-name
```

For more information about buckets, see [Working with S3 on Outposts buckets](S3OutpostsWorkingBuckets.md)\. 

### Objects<a name="BasicsObjectsS3Outposts"></a>

Objects are the fundamental entities stored in S3 on Outposts\. Objects consist of object data and metadata\. The metadata is a set of name\-value pairs that describe the object\. These pairs include some default metadata, such as the date last modified, and standard HTTP metadata, such as `Content-Type`\. You can also specify custom metadata at the time that the object is stored\. An object is uniquely identified within a bucket by a [key \(or name\)](Welcome.md#BasicsKeys)\.

With Amazon S3 on Outposts, object data is always stored on the Outpost\. When AWS installs an Outpost rack, your data stays local to your Outpost to meet data\-residency requirements\. Your objects never leave your Outpost and are not in an AWS Region\. Because the AWS Management Console is hosted in\-Region, you can't use the console to upload or manage objects in your Outpost\. However, you can use the REST API, AWS Command Line Interface \(AWS CLI\), and AWS SDKs to upload and manage your objects through your access points\.

### Keys<a name="BasicsKeysS3Outposts"></a>

An *object key* \(or *key name*\) is the unique identifier for an object within a bucket\. Every object in a bucket has exactly one key\. The combination of a bucket and object key uniquely identifies each object\. 

The following example shows the ARN format for S3 on Outposts objects, which includes the AWS Region code for the Region that the Outpost is homed to, AWS account ID, Outpost ID, bucket name, and object key:

```
arn:aws:s3-outposts:us-west-2:123456789012:​outpost/op-01ac5d28a6a232904/bucket/DOC-EXAMPLE-BUCKET1/object/myobject
```

For more information about object keys, see [Working with S3 on Outposts objects](S3OutpostsWorkingObjects.md)\. 

### S3 Versioning<a name="S3OutpostsVersioning"></a>

You can use S3 Versioning on Outposts buckets to keep multiple variants of an object in the same bucket\. With S3 Versioning, you can preserve, retrieve, and restore every version of every object stored in your buckets\. S3 Versioning helps you recover from unintended user actions and application failures\. 

For more information, see [Managing S3 Versioning for your S3 on Outposts bucket](S3OutpostsManagingVersioning.md)\.

### Version ID<a name="S3OutpostsVersionID"></a>

When you enable S3 Versioning in a bucket, S3 on Outposts generates a unique version ID for each object added to the bucket\. Objects that already existed in the bucket at the time that you enable versioning have a version ID of `null`\. If you modify these \(or any other\) objects with other operations, such as [PutObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html), the new objects get a unique version ID\.

For more information, see [Managing S3 Versioning for your S3 on Outposts bucket](S3OutpostsManagingVersioning.md)\.

### Storage class and encryption<a name="S3OutpostsStorageClasses"></a>

S3 on Outposts provides a new storage class, S3 Outposts \(`OUTPOSTS`\)\. The S3 Outposts storage class is available only for objects stored in buckets on AWS Outposts\. If you try to use other S3 storage classes with S3 on Outposts, S3 on Outposts returns the `InvalidStorageClass` error\. 

By default, objects stored in the S3 Outposts \(`OUTPOSTS`\) storage class are encrypted using server\-side encryption with Amazon S3 managed encryption keys \(SSE\-S3\)\. For more information, see [Data encryption in S3 on Outposts](s3-outposts-data-encryption.md)\. 

### Bucket policy<a name="S3OutpostsBucketPolicies"></a>

A bucket policy is a resource\-based AWS Identity and Access Management \(IAM\) policy that you can use to grant access permissions to your bucket and the objects in it\. Only the bucket owner can associate a policy with a bucket\. The permissions attached to the bucket apply to all of the objects in the bucket that are owned by the bucket owner\. Bucket policies are limited to 20 KB in size\.

Bucket policies use JSON\-based IAM policy language that is standard across AWS\. You can use bucket policies to add or deny permissions for the objects in a bucket\. Bucket policies allow or deny requests based on the elements in the policy\. These elements can include the requester, S3 on Outposts actions, resources, and aspects or conditions of the request \(for example, the IP address used to make the request\)\. For example, you can create a bucket policy that grants cross\-account permissions to upload objects to an S3 on Outposts bucket while ensuring that the bucket owner has full control of the uploaded objects\. For more information, see [Bucket policy examples](example-bucket-policies.md)\.

In your bucket policy, you can use wildcard characters \(`*`\) in ARNs and other values to grant permissions to a subset of objects\. For example, you can control access to groups of objects that begin with a common [prefix](https://docs.aws.amazon.com/general/latest/gr/glos-chap.html#keyprefix) or end with a given extension, such as `.html`\.

### S3 on Outposts access points<a name="BasicsAccessPointsS3Outposts"></a>

S3 on Outposts access points are named network endpoints with dedicated access policies that describe how data can be accessed using that endpoint\. Access points simplify managing data access at scale for shared datasets in S3 on Outposts\. Access points are attached to buckets that you can use to perform S3 object operations, such as `GetObject` and `PutObject`\. 

When you specify the bucket for object operations, you use the access point ARN or access point alias\. For more information about access point aliases, see [Using a bucket\-style alias for your S3 on Outposts bucket access point](s3-outposts-access-points-alias.md)\.

Access points have distinct permissions and network controls that S3 on Outposts applies for any request that is made through that access point\. Each access point enforces a customized access point policy that works in conjunction with the bucket policy that is attached to the underlying bucket\.

For more information, see [Accessing your S3 on Outposts buckets and objects](S3OutpostsNetworking.md#AccessingS3Outposts)\.

## Features of S3 on Outposts<a name="S3OutpostsFeatures"></a>

### Access management<a name="s3-outposts-features-access-management"></a>

S3 on Outposts provides features for auditing and managing access to your buckets and objects\. By default, S3 on Outposts buckets and the objects in them are private\. You have access only to the S3 on Outposts resources that you create\. 

To grant granular resource permissions that support your specific use case or to audit the permissions of your S3 on Outposts resources, you can use the following features\. 
+ [S3 Block Public Access](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-control-block-public-access.html) – Block public access to buckets and objects\. For buckets on Outposts, Block Public Access is always enabled by default\.
+ [AWS Identity and Access Management \(IAM\)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/S3OutpostsIAM.html) – IAM is a web service that helps you securely control access to AWS resources, including your S3 on Outposts resources\. With IAM, you can centrally manage permissions that control which AWS resources users can access\. You use IAM to control who is authenticated \(signed in\) and authorized \(has permissions\) to use resources\.
+ [S3 on Outposts access points](https://docs.aws.amazon.com/AmazonS3/latest/userguide/S3OutpostsWorkingBuckets.html) – Manage data access for shared datasets in S3 on Outposts\. Access points are named network endpoints with dedicated access policies\. Access points are attached to buckets and can be used to perform object operations, such as `GetObject` and `PutObject`\.
+ [Bucket policies](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucket-policies.html) – Use IAM\-based policy language to configure resource\-based permissions for your S3 buckets and the objects in them\.
+ [AWS Resource Access Manager \(AWS RAM\)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/outposts-sharing-with-ram.html) – Securely share your S3 on Outposts capacity across AWS accounts, within your organization or organizational units \(OUs\) in AWS Organizations\. 

### Storage logging and monitoring<a name="s3-outposts-features-storage-monitoring"></a>

S3 on Outposts provides logging and monitoring tools that you can use to monitor and control how your S3 on Outposts resources are being used\. For more information, see [Monitoring tools](https://docs.aws.amazon.com/AmazonS3/latest/userguide/S3OutpostsManaging.html)\.
+ [Amazon CloudWatch metrics for S3 on Outposts ](https://docs.aws.amazon.com/AmazonS3/latest/userguide/S3OutpostsCapacity.html#S3OutpostsCloudWatchMetrics) – Track the operational health of your resources and understand your capacity availability\. 
+ [Amazon CloudWatch Events events for S3 on Outposts ](https://docs.aws.amazon.com/AmazonS3/latest/userguide/S3OutpostsNotificationsCWE.html) – Create a rule for any S3 on Outposts API event to receive notifications through all supported CloudWatch Events targets, including Amazon Simple Queue Service \(Amazon SQS\), Amazon Simple Notification Service \(Amazon SNS\), and AWS Lambda\. 
+ [AWS CloudTrail logs for S3 on Outposts](https://docs.aws.amazon.com/AmazonS3/latest/userguide/S3OutpostsCloudtrail.html) – Record actions taken by a user, a role, or an AWS service in S3 on Outposts\. CloudTrail logs provide you with detailed API tracking for S3 bucket\-level and object\-level operations\.

### Strong consistency<a name="s3-outposts-features-strong-consistency"></a>

S3 on Outposts provides strong read\-after\-write consistency for PUT and DELETE requests of objects in your S3 on Outposts bucket in all AWS Regions\. This behavior applies to both writes of new objects and to PUT requests that overwrite existing objects and to DELETE requests\. In addition, S3 on Outposts object tags and object metadata \(for example, the HEAD object\) are strongly consistent\. For more information, see [Amazon S3 data consistency model](Welcome.md#ConsistencyModel)\.

## Related services<a name="S3OutpostsRelatedAmazonWebServices"></a>

After you load your data into S3 on Outposts, you can use it with other AWS services\. The following are the services that you might use most frequently:
+ [Amazon Elastic Compute Cloud \(Amazon EC2\)](https://docs.aws.amazon.com/ec2/index.html) – Provides secure and scalable computing capacity in the AWS Cloud\. Using Amazon EC2 lessens your need to invest in hardware up front, so you can develop and deploy applications faster\. You can use Amazon EC2 to launch as many or as few virtual servers as you need, configure security and networking, and manage storage\.
+ [Amazon Elastic Block Store \(Amazon EBS\) on Outposts](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/snapshots-outposts.html) – Use Amazon EBS local snapshots on Outposts to store snapshots of volumes on an Outpost locally in S3 on Outposts\.
+ [Amazon Relational Database Service \(Amazon RDS\) on Outposts](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/rds-on-outposts.html) – Use Amazon RDS local backups to store your Amazon RDS backups locally on your Outpost\.
+ [AWS DataSync](https://docs.aws.amazon.com/datasync/latest/userguide/getting-started.html) – Automate transferring data between your Outposts and AWS Regions, choosing what to transfer, when to transfer, and how much network bandwidth to use\. S3 on Outposts is integrated with AWS DataSync\. For on\-premises applications that require high\-throughput local processing, S3 on Outposts provides on\-premises object storage to minimize data transfers and buffer from network variations, while providing you the ability to easily transfer data between Outposts and AWS Regions\.

## Accessing S3 on Outposts<a name="accessing-s3-outposts"></a>

You can work with S3 on Outposts in any of the following ways:

### AWS Management Console<a name="access-outposts-aws-management-console"></a>

The console is a web\-based user interface for managing S3 on Outposts and AWS resources\. If you've signed up for an AWS account, you can access S3 on Outposts by signing into the AWS Management Console and choosing **S3** from the AWS Management Console home page\. Then, choose **Outposts buckets** from the left navigation pane\.

### AWS Command Line Interface<a name="access-outposts-aws-cli"></a>

You can use the AWS command line tools to issue commands or build scripts at your system's command line to perform AWS \(including S3\) tasks\.

The [AWS Command Line Interface \(AWS CLI\)](http://aws.amazon.com/cli/) provides commands for a broad set of AWS services\. The AWS CLI is supported on Windows, macOS, and Linux\. To get started, see the [AWS Command Line Interface User Guide](https://docs.aws.amazon.com/cli/latest/userguide/)\. For more information about the commands that you can use with S3 on Outposts, see [s3api](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/index.html), [s3control](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3control/index.html), and [s3outposts](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3outposts/index.html) in the *AWS CLI Command Reference*\.

### AWS SDKs<a name="access-s3-outposts-aws-sdks"></a>

AWS provides SDKs \(software development kits\) that consist of libraries and sample code for various programming languages and platforms \(Java, Python, Ruby, \.NET, iOS, Android, and so on\)\. The AWS SDKs provide a convenient way to create programmatic access to S3 on Outposts and AWS\. Because S3 on Outposts uses the same SDKs as Amazon S3, S3 on Outposts provides a consistent experience using the same S3 APIs, automation, and tools\. 

S3 on Outposts is a REST service\. You can send requests to S3 on Outposts by using the AWS SDK libraries, which wrap the underlying REST API and simplify your programming tasks\. For example, the SDKs take care of tasks such as calculating signatures, cryptographically signing requests, managing errors, and retrying requests automatically\. For information about the AWS SDKs, including how to download and install them, see [Tools to Build on AWS](http://aws.amazon.com/tools/)\.

## Paying for S3 on Outposts<a name="S3OutpostsPayingforStorage"></a>

You can purchase a variety of AWS Outposts rack configurations featuring a combination of Amazon EC2 instance types, Amazon EBS General Purpose solid state drive \(SSD\) volumes \(`gp2`\), and S3 on Outposts\. Pricing includes delivery, installation, infrastructure service maintenance, and software patches and upgrades\.

For more information, see [AWS Outposts rack pricing](http://aws.amazon.com/outposts/rack/pricing/)\.

## Next steps<a name="S3OutpostsNextSteps"></a>

For more information about working with S3 on Outposts, see the following topics:
+ [Setting up your Outpost](OrderOutposts.md)
+ [How is Amazon S3 on Outposts different from Amazon S3?](S3OnOutpostsRestrictionsLimitations.md)
+ [Getting started with Amazon S3 on Outposts](S3OutpostsGS.md)
+ [Networking for S3 on Outposts](S3OutpostsNetworking.md)
+ [Working with S3 on Outposts buckets](S3OutpostsWorkingBuckets.md)
+ [Working with S3 on Outposts objects](S3OutpostsWorkingObjects.md)
+ [Security in S3 on Outposts](s3outposts-security.md)
+ [Managing S3 on Outposts storage ](S3OutpostsManaging.md)
+ [Developing with Amazon S3 on Outposts](S3OutpostsDeveloping.md)