# What is Amazon S3?<a name="Welcome"></a>

Amazon Simple Storage Service \(Amazon S3\) is an object storage service that offers industry\-leading scalability, data availability, security, and performance\. Customers of all sizes and industries can use Amazon S3 to store and protect any amount of data for a range of use cases, such as data lakes, websites, mobile applications, backup and restore, archive, enterprise applications, IoT devices, and big data analytics\. Amazon S3 provides management features so that you can optimize, organize, and configure access to your data to meet your specific business, organizational, and compliance requirements\.

**Topics**
+ [Features of Amazon S3](#S3Features)
+ [How Amazon S3 works](#CoreConcepts)
+ [Amazon S3 data consistency model](#ConsistencyModel)
+ [Related services](#RelatedAmazonWebServices)
+ [Accessing Amazon S3](#API)
+ [Paying for Amazon S3](#PayingforStorage)
+ [PCI DSS compliance](#pci-dss-compliance)

## Features of Amazon S3<a name="S3Features"></a>

### Storage classes<a name="RRS"></a>

Amazon S3 offers a range of storage classes designed for different use cases\. For example, you can store mission\-critical production data in S3 Standard for frequent access, save costs by storing infrequently accessed data in S3 Standard\-IA or S3 One Zone\-IA, and archive data at the lowest costs in S3 Glacier Instant Retrieval, S3 Glacier Flexible Retrieval, and S3 Glacier Deep Archive\. 

You can store data with changing or unknown access patterns in S3 Intelligent\-Tiering, which optimizes storage costs by automatically moving your data between four access tiers when your access patterns change\. These four access tiers include two low\-latency access tiers optimized for frequent and infrequent access, and two opt\-in archive access tiers designed for asynchronous access for rarely accessed data\.

For more information, see [Using Amazon S3 storage classes](storage-class-intro.md)\. For more information about S3 Glacier Flexible Retrieval, see the [https://docs.aws.amazon.com/amazonglacier/latest/dev/introduction.html](https://docs.aws.amazon.com/amazonglacier/latest/dev/introduction.html)\.

### Storage management<a name="features-storage-management"></a>

Amazon S3 has storage management features that you can use to manage costs, meet regulatory requirements, reduce latency, and save multiple distinct copies of your data for compliance requirements\.
+ [S3 Lifecycle](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.html) – Configure a lifecycle policy to manage your objects and store them cost effectively throughout their lifecycle\. You can transition objects to other S3 storage classes or expire objects that reach the end of their lifetimes\.
+ [S3 Object Lock](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lock.html) – Prevent Amazon S3 objects from being deleted or overwritten for a fixed amount of time or indefinitely\. You can use Object Lock to help meet regulatory requirements that require *write\-once\-read\-many* *\(WORM\)* storage or to simply add another layer of protection against object changes and deletions\.
+ [S3 Replication](https://docs.aws.amazon.com/AmazonS3/latest/userguide/replication.html) – Replicate objects and their respective metadata and object tags to one or more destination buckets in the same or different AWS Regions for reduced latency, compliance, security, and other use cases\. 
+ [S3 Batch Operations](https://docs.aws.amazon.com/AmazonS3/latest/userguide/batch-ops.html) – Manage billions of objects at scale with a single S3 API request or a few clicks in the Amazon S3 console\. You can use Batch Operations to perform operations such as **Copy**, **Invoke AWS Lambda function**, and **Restore** on millions or billions of objects\.

### Access management<a name="features-access-management"></a>

Amazon S3 provides features for auditing and managing access to your buckets and objects\. By default, S3 buckets and the objects in them are private\. You have access only to the S3 resources that you create\. To grant granular resource permissions that support your specific use case or to audit the permissions of your Amazon S3 resources, you can use the following features\. 
+ [S3 Block Public Access](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-control-block-public-access.html) – Block public access to S3 buckets and objects\. By default, Block Public Access settings are turned on at the account and bucket level\.
+ [AWS Identity and Access Management \(IAM\)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-access-control.html) – Create IAM users for your AWS account to manage access to your Amazon S3 resources\. For example, you can use IAM with Amazon S3 to control the type of access a user or group of users has to an S3 bucket that your AWS account owns\.
+ [Bucket policies](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucket-policies.html) – Use IAM\-based policy language to configure resource\-based permissions for your S3 buckets and the objects in them\.
+  [Amazon S3 access points](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-points.html) – Configure named network endpoints with dedicated access policies to manage data access at scale for shared datasets in Amazon S3\. 
+ [Access control lists \(ACLs\)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/acls.html) – Grant read and write permissions for individual buckets and objects to authorized users\. As a general rule, we recommend using S3 resource\-based policies \(bucket policies and access point policies\) or IAM policies for access control instead of ACLs\. ACLs are an access control mechanism that predates resource\-based policies and IAM\. For more information about when you'd use ACLs instead of resource\-based policies or IAM policies, see [Access policy guidelines](access-policy-alternatives-guidelines.md)\.
+ [S3 Object Ownership](https://docs.aws.amazon.com/AmazonS3/latest/userguide/about-object-ownership.html) – Disable ACLs and take ownership of every object in your bucket, simplifying access management for data stored in Amazon S3\. You, as the bucket owner, automatically own and have full control over every object in your bucket, and access control for your data is based on policies\.
+ [Access Analyzer for S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-analyzer.html) – Evaluate and monitor your S3 bucket access policies, ensuring that the policies provide only the intended access to your S3 resources\. 

### Data processing<a name="features-data-processing"></a>

To transform data and trigger workflows to automate a variety of other processing activities at scale, you can use the following features\.
+ [S3 Object Lambda](https://docs.aws.amazon.com/AmazonS3/latest/userguide/transforming-objects.html) – Add your own code to S3 GET, HEAD, and LIST requests to modify and process data as it is returned to an application\. Filter rows, dynamically resize images, redact confidential data, and much more\.
+ [Event notifications](https://docs.aws.amazon.com/AmazonS3/latest/userguide/NotificationHowTo.html) – Trigger workflows that use Amazon Simple Notification Service \(Amazon SNS\), Amazon Simple Queue Service \(Amazon SQS\), and AWS Lambda when a change is made to your S3 resources\.

### Storage logging and monitoring<a name="features-storage-monitoring"></a>

Amazon S3 provides logging and monitoring tools that you can use to monitor and control how your Amazon S3 resources are being used\. For more information, see [Monitoring tools](https://docs.aws.amazon.com/AmazonS3/latest/userguide/monitoring-automated-manual.html)\.

**Automated monitoring tools**
+ [Amazon CloudWatch metrics for Amazon S3 ](https://docs.aws.amazon.com/AmazonS3/latest/userguide/cloudwatch-monitoring.html) – Track the operational health of your S3 resources and configure billing alerts when estimated charges reach a user\-defined threshold\. 
+ [AWS CloudTrail](https://docs.aws.amazon.com/AmazonS3/latest/userguide/cloudtrail-logging.html) – Record actions taken by a user, a role, or an AWS service in Amazon S3\. CloudTrail logs provide you with detailed API tracking for S3 bucket\-level and object\-level operations\.

**Manual monitoring tools**
+ [Server access logging](https://docs.aws.amazon.com/AmazonS3/latest/userguide/ServerLogs.html) – Get detailed records for the requests that are made to a bucket\. You can use server access logs for many use cases, such as conducting security and access audits, learning about your customer base, and understanding your Amazon S3 bill\.
+ [AWS Trusted Advisor](https://docs.aws.amazon.com/awssupport/latest/user/trusted-advisor.html) – Evaluate your account by using AWS best practice checks to identify ways to optimize your AWS infrastructure, improve security and performance, reduce costs, and monitor service quotas\. You can then follow the recommendations to optimize your services and resources\.

### Analytics and insights<a name="features-analytics-insights"></a>

Amazon S3 offers features to help you gain visibility into your storage usage, which empowers you to better understand, analyze, and optimize your storage at scale\.
+ [Amazon S3 Storage Lens](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage_lens.html) – Understand, analyze, and optimize your storage\. S3 Storage Lens provides 29\+ usage and activity metrics and interactive dashboards to aggregate data for your entire organization, specific accounts, AWS Regions, buckets, or prefixes\.
+ [Storage Class Analysis](https://docs.aws.amazon.com/AmazonS3/latest/userguide/analytics-storage-class.html) – Analyze storage access patterns to decide when it's time to move data to a more cost\-effective storage class\. 
+ [S3 Inventory with Inventory reports](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-inventory.html) – Audit and report on objects and their corresponding metadata and configure other Amazon S3 features to take action in Inventory reports\. For example, you can report on the replication and encryption status of your objects\. For a list of all the metadata available for each object in Inventory reports, see [Amazon S3 Inventory list](storage-inventory.md#storage-inventory-contents)\.

### Strong consistency<a name="features-strong-consistency"></a>

Amazon S3 provides strong read\-after\-write consistency for PUT and DELETE requests of objects in your Amazon S3 bucket in all AWS Regions\. This behavior applies to both writes of new objects as well as PUT requests that overwrite existing objects and DELETE requests\. In addition, read operations on Amazon S3 Select, Amazon S3 access control lists \(ACLs\), Amazon S3 Object Tags, and object metadata \(for example, the HEAD object\) are strongly consistent\. For more information, see [Amazon S3 data consistency model](#ConsistencyModel)\.

## How Amazon S3 works<a name="CoreConcepts"></a>

Amazon S3 is an object storage service that stores data as objects within buckets\. An *object* is a file and any metadata that describes the file\. A *bucket* is a container for objects\. 

To store your data in Amazon S3, you first create a bucket and specify a bucket name and AWS Region\. Then, you upload your data to that bucket as objects in Amazon S3\. Each object has a *key* \(or *key name*\), which is the unique identifier for the object within the bucket\.

S3 provides features that you can configure to support your specific use case\. For example, you can use S3 Versioning to keep multiple versions of an object in the same bucket, which allows you to restore objects that are accidentally deleted or overwritten\.

Buckets and the objects in them are private and can be accessed only if you explicitly grant access permissions\. You can use bucket policies, AWS Identity and Access Management \(IAM\) policies, access control lists \(ACLs\), and S3 Access Points to manage access\.

**Topics**
+ [Buckets](#BasicsBucket)
+ [Objects](#BasicsObjects)
+ [Keys](#BasicsKeys)
+ [S3 Versioning](#Versions)
+ [Version ID](#BasicsVersionID)
+ [Bucket policy](#BucketPolicies)
+ [S3 Access Points](#BasicsAccessPoints)
+ [Access control lists \(ACLs\)](#S3_ACLs)
+ [Regions](#Regions)

### Buckets<a name="BasicsBucket"></a>

A bucket is a container for objects stored in Amazon S3\. You can store any number of objects in a bucket and can have up to 100 buckets in your account\. To request an increase, visit the [Service Quotas Console](https://console.aws.amazon.com/servicequotas/home/services/s3/quotas/)\.

Every object is contained in a bucket\. For example, if the object named `photos/puppy.jpg` is stored in the `DOC-EXAMPLE-BUCKET` bucket in the US West \(Oregon\) Region, then it is addressable using the URL `https://DOC-EXAMPLE-BUCKET.s3.us-west-2.amazonaws.com/photos/puppy.jpg`\. For more information, see [Accessing a Bucket](access-bucket-intro.md)\. 

When you create a bucket, you enter a bucket name and choose the AWS Region where the bucket will reside\. After you create a bucket, you cannot change the name of the bucket or its Region\. Bucket names must follow the [bucket naming rules](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucketnamingrules.html)\. You can also configure a bucket to use [S3 Versioning](Versioning.md) or other [storage management](https://docs.aws.amazon.com/AmazonS3/latest/userguide/managing-storage.html) features\.

 Buckets also:
+ Organize the Amazon S3 namespace at the highest level\.
+ Identify the account responsible for storage and data transfer charges\.
+ Provide access control options, such as bucket policies, access control lists \(ACLs\), and S3 Access Points, that you can use to manage access to your Amazon S3 resources\.
+ Serve as the unit of aggregation for usage reporting\.

 For more information about buckets, see [Buckets overview](UsingBucket.md)\. 

### Objects<a name="BasicsObjects"></a>

Objects are the fundamental entities stored in Amazon S3\. Objects consist of object data and metadata\. The metadata is a set of name\-value pairs that describe the object\. These pairs include some default metadata, such as the date last modified, and standard HTTP metadata, such as `Content-Type`\. You can also specify custom metadata at the time that the object is stored\.

An object is uniquely identified within a bucket by a [key \(name\)](#BasicsKeys) and a [version ID](#BasicsVersionID) \(if S3 Versioning is enabled on the bucket\)\. For more information about objects, see [Amazon S3 objects overview](UsingObjects.md)\.

### Keys<a name="BasicsKeys"></a>

An *object key* \(or *key name*\) is the unique identifier for an object within a bucket\. Every object in a bucket has exactly one key\. The combination of a bucket, object key, and optionally, version ID \(if S3 Versioning is enabled for the bucket\) uniquely identify each object\. So you can think of Amazon S3 as a basic data map between "bucket \+ key \+ version" and the object itself\. 

Every object in Amazon S3 can be uniquely addressed through the combination of the web service endpoint, bucket name, key, and optionally, a version\. For example, in the URL `https://DOC-EXAMPLE-BUCKET.s3.us-west-2.amazonaws.com/photos/puppy.jpg`, `DOC-EXAMPLE-BUCKET` is the name of the bucket and `photos/puppy.jpg` is the key\.

For more information about object keys, see [Creating object key names](object-keys.md)\. 

### S3 Versioning<a name="Versions"></a>

You can use S3 Versioning to keep multiple variants of an object in the same bucket\. With S3 Versioning, you can preserve, retrieve, and restore every version of every object stored in your buckets\. You can easily recover from both unintended user actions and application failures\.

For more information, see [Using versioning in S3 buckets](Versioning.md)\.

### Version ID<a name="BasicsVersionID"></a>

When you enable S3 Versioning in a bucket, Amazon S3 generates a unique version ID for each object added to the bucket\. Objects that already existed in the bucket at the time that you enable versioning have a version ID of `null`\. If you modify these \(or any other\) objects with other operations, such as [CopyObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html) and [PutObject](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html), the new objects get a unique version ID\.

For more information, see [Using versioning in S3 buckets](Versioning.md)\.

### Bucket policy<a name="BucketPolicies"></a>

A bucket policy is a resource\-based AWS Identity and Access Management \(IAM\) policy that you can use to grant access permissions to your bucket and the objects in it\. Only the bucket owner can associate a policy with a bucket\. The permissions attached to the bucket apply to all of the objects in the bucket that are owned by the bucket owner\. Bucket policies are limited to 20 KB in size\.

Bucket policies use JSON\-based access policy language that is standard across AWS\. You can use bucket policies to add or deny permissions for the objects in a bucket\. Bucket policies allow or deny requests based on the elements in the policy, including the requester, S3 actions, resources, and aspects or conditions of the request \(for example, the IP address used to make the request\)\. For example, you can create a bucket policy that grants cross\-account permissions to upload objects to an S3 bucket while ensuring that the bucket owner has full control of the uploaded objects\. For more information, see [Bucket policy examples](example-bucket-policies.md)\.

In your bucket policy, you can use wildcard characters on Amazon Resource Names \(ARNs\) and other values to grant permissions to a subset of objects\. For example, you can control access to groups of objects that begin with a common [prefix](https://docs.aws.amazon.com/general/latest/gr/glos-chap.html#keyprefix) or end with a given extension, such as `.html`\.

### S3 Access Points<a name="BasicsAccessPoints"></a>

Amazon S3 Access Points are named network endpoints with dedicated access policies that describe how data can be accessed using that endpoint\. Access Points are attached to buckets that you can use to perform S3 object operations, such as GetObject and PutObject\. Access Points simplify managing data access at scale for shared datasets in Amazon S3\. 

Each access point has its own access point policy\. You can configure [Block Public Access](access-control-block-public-access.md) settings for each access point\. To restrict Amazon S3 data access to a private network, you can also configure any access point to accept requests only from a virtual private cloud \(VPC\)\.

For more information, see [Managing data access with Amazon S3 access points](access-points.md)\. 

### Access control lists \(ACLs\)<a name="S3_ACLs"></a>

You can use ACLs to grant read and write permissions to authorized users for individual buckets and objects\. Each bucket and object has an ACL attached to it as a subresource\. The ACL defines which AWS accounts or groups are granted access and the type of access\. ACLs are an access control mechanism that predates IAM\. For more information about ACLs, see [Access control list \(ACL\) overview](acl-overview.md)\.

By default, when another AWS account uploads an object to your S3 bucket, that account \(the object writer\) owns the object, has access to it, and can grant other users access to it through ACLs\. You can use Object Ownership to change this default behavior so that ACLs are disabled and you, as the bucket owner, automatically own every object in your bucket\. As a result, access control for your data is based on policies, such as IAM policies, S3 bucket policies, virtual private cloud \(VPC\) endpoint policies, and AWS Organizations service control policies \(SCPs\)\.

A majority of modern use cases in Amazon S3 no longer require the use of ACLs, and we recommend that you disable ACLs except in unusual circumstances where you need to control access for each object individually\. With Object Ownership, you can disable ACLs and rely on policies for access control\. When you disable ACLs, you can easily maintain a bucket with objects uploaded by different AWS accounts\. You, as the bucket owner, own all the objects in the bucket and can manage access to them using policies\. For more information, see [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\.

### Regions<a name="Regions"></a>

You can choose the geographical AWS Region where Amazon S3 stores the buckets that you create\. You might choose a Region to optimize latency, minimize costs, or address regulatory requirements\. Objects stored in an AWS Region never leave the Region unless you explicitly transfer or replicate them to another Region\. For example, objects stored in the Europe \(Ireland\) Region never leave it\. 

**Note**  
You can access Amazon S3 and its features only in the AWS Regions that are enabled for your account\. For more information about enabling a Region to create and manage AWS resources, see [Managing AWS Regions](https://docs.aws.amazon.com/general/latest/gr/rande-manage.html) in the *AWS General Reference*\.

For a list of Amazon S3 Regions and endpoints, see [Regions and endpoints](https://docs.aws.amazon.com/general/latest/gr/s3.html) in the *AWS General Reference*\. 

## Amazon S3 data consistency model<a name="ConsistencyModel"></a>

Amazon S3 provides strong read\-after\-write consistency for PUT and DELETE requests of objects in your Amazon S3 bucket in all AWS Regions\. This behavior applies to both writes to new objects as well as PUT requests that overwrite existing objects and DELETE requests\. In addition, read operations on Amazon S3 Select, Amazon S3 access controls lists \(ACLs\), Amazon S3 Object Tags, and object metadata \(for example, the HEAD object\) are strongly consistent\. 

Updates to a single key are atomic\. For example, if you make a PUT request to an existing key from one thread and perform a GET request on the same key from a second thread concurrently, you will get either the old data or the new data, but never partial or corrupt data\.

Amazon S3 achieves high availability by replicating data across multiple servers within AWS data centers\. If a PUT request is successful, your data is safely stored\. Any read \(GET or LIST request\) that is initiated following the receipt of a successful PUT response will return the data written by the PUT request\. Here are examples of this behavior: 
+ A process writes a new object to Amazon S3 and immediately lists keys within its bucket\. The new object appears in the list\.
+ A process replaces an existing object and immediately tries to read it\. Amazon S3 returns the new data\. 
+ A process deletes an existing object and immediately tries to read it\. Amazon S3 does not return any data because the object has been deleted\. 
+ A process deletes an existing object and immediately lists keys within its bucket\. The object does not appear in the listing\. 

**Note**  
Amazon S3 does not support object locking for concurrent writers\. If two PUT requests are simultaneously made to the same key, the request with the latest timestamp wins\. If this is an issue, you must build an object\-locking mechanism into your application\. 
Updates are key\-based\. There is no way to make atomic updates across keys\. For example, you cannot make the update of one key dependent on the update of another key unless you design this functionality into your application\.

Bucket configurations have an eventual consistency model\. Specifically, this means that:
+ If you delete a bucket and immediately list all buckets, the deleted bucket might still appear in the list\.
+ If you enable versioning on a bucket for the first time, it might take a short amount of time for the change to be fully propagated\. We recommend that you wait for 15 minutes after enabling versioning before issuing write operations \(PUT or DELETE requests\) on objects in the bucket\.

### Concurrent applications<a name="ApplicationConcurrency"></a>

This section provides examples of behavior to be expected from Amazon S3 when multiple clients are writing to the same items\.

In this example, both W1 \(write 1\) and W2 \(write 2\) finish before the start of R1 \(read 1\) and R2 \(read 2\)\. Because S3 is strongly consistent, R1 and R2 both return `color = ruby`\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/consistency1.png)

In the next example, W2 does not finish before the start of R1\. Therefore, R1 might return `color = ruby` or `color = garnet`\. However, because W1 and W2 finish before the start of R2, R2 returns `color = garnet`\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/consistency2.png)

In the last example, W2 begins before W1 has received an acknowledgement\. Therefore, these writes are considered concurrent\. Amazon S3 internally uses last\-writer\-wins semantics to determine which write takes precedence\. However, the order in which Amazon S3 receives the requests and the order in which applications receive acknowledgements cannot be predicted because of various factors, such as network latency\. For example, W2 might be initiated by an Amazon EC2 instance in the same Region, while W1 might be initiated by a host that is farther away\. The best way to determine the final value is to perform a read after both writes have been acknowledged\. 

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/consistency3.png)

## Related services<a name="RelatedAmazonWebServices"></a>

After you load your data into Amazon S3, you can use it with other AWS services\. The following are the services that you might use most frequently:
+ **[Amazon Elastic Compute Cloud \(Amazon EC2\)](https://aws.amazon.com/ec2/)** – Provides secure and scalable computing capacity in the AWS Cloud\. Using Amazon EC2 eliminates your need to invest in hardware up front, so you can develop and deploy applications faster\. You can use Amazon EC2 to launch as many or as few virtual servers as you need, configure security and networking, and manage storage\.
+ **[Amazon EMR](https://aws.amazon.com/elasticmapreduce/)** – Helps businesses, researchers, data analysts, and developers easily and cost\-effectively process vast amounts of data\. Amazon EMR uses a hosted Hadoop framework running on the web\-scale infrastructure of Amazon EC2 and Amazon S3\. 
+ **[AWS Snow Family](http://aws.amazon.com/snow/)** – Helps customers that need to run operations in austere, non\-data center environments, and in locations where there's a lack of consistent network connectivity\. You can use AWS Snow Family devices to locally and cost\-effectively access the storage and compute power of the AWS Cloud in places where an internet connection might not be an option\. 
+ **[AWS Transfer Family](http://aws.amazon.com/aws-transfer-family/)** – Provides fully managed support for file transfers directly into and out of Amazon S3 or Amazon Elastic File System \(Amazon EFS\) using Secure Shell \(SSH\) File Transfer Protocol \(SFTP\), File Transfer Protocol over SSL \(FTPS\), and File Transfer Protocol \(FTP\)\. 

## Accessing Amazon S3<a name="API"></a>

You can work with Amazon S3 in any of the following ways:

### AWS Management Console<a name="access-aws-management-console"></a>

The console is a web\-based user interface for managing Amazon S3 and AWS resources\. If you've signed up for an AWS account, you can access the Amazon S3 console by signing into the AWS Management Console and choosing **S3** from the AWS Management Console home page\.

### AWS Command Line Interface<a name="access-aws-cli"></a>

You can use the AWS command line tools to issue commands or build scripts at your system's command line to perform AWS \(including S3\) tasks\.

The [AWS Command Line Interface \(AWS CLI\)](http://aws.amazon.com/cli/) provides commands for a broad set of AWS services\. The AWS CLI is supported on Windows, macOS, and Linux\. To get started, see the [https://docs.aws.amazon.com/cli/latest/userguide/](https://docs.aws.amazon.com/cli/latest/userguide/)\. For more information about the commands for Amazon S3, see [s3api](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/index.html) and [s3control](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3control/index.html) in the *AWS CLI Command Reference*\.

### AWS SDKs<a name="access-aws-sdks"></a>

AWS provides SDKs \(software development kits\) that consist of libraries and sample code for various programming languages and platforms \(Java, Python, Ruby, \.NET, iOS, Android, and so on\)\. The AWS SDKs provide a convenient way to create programmatic access to S3 and AWS\. Amazon S3 is a REST service\. You can send requests to Amazon S3 using the AWS SDK libraries\. which wrap the underlying Amazon S3 REST API and simplify your programming tasks\. For example, the SDKs take care of tasks such as calculating signatures, cryptographically signing requests, managing errors, and retrying requests automatically\. For information about the AWS SDKs, including how to download and install them, see [Tools for AWS](http://aws.amazon.com/tools/)\.

Every interaction with Amazon S3 is either authenticated or anonymous\. If you are using the AWS SDKs, the libraries compute the signature for authentication from the keys that you provide\. For more information about how to make requests to Amazon S3, see [Making requests](MakingRequests.md)\.

### Amazon S3 REST API<a name="UsingRESTAPI"></a>

The architecture of Amazon S3 is designed to be programming language\-neutral, using AWS\-supported interfaces to store and retrieve objects\. You can access S3 and AWS programmatically by using the Amazon S3 REST API\. The REST API is an HTTP interface to Amazon S3\. With the REST API, you use standard HTTP requests to create, fetch, and delete buckets and objects\.

To use the REST API, you can use any toolkit that supports HTTP\. You can even use a browser to fetch objects, as long as they are anonymously readable\.

The REST API uses standard HTTP headers and status codes, so that standard browsers and toolkits work as expected\. In some areas, we have added functionality to HTTP \(for example, we added headers to support access control\)\. In these cases, we have done our best to add the new functionality in a way that matches the style of standard HTTP usage\.

If you make direct REST API calls in your application, you must write the code to compute the signature and add it to the request\. For more information about how to make requests to Amazon S3, see [Making requests](MakingRequests.md)\.

**Note**  
SOAP API support over HTTP is deprecated, but it is still available over HTTPS\. Newer Amazon S3 features are not supported for SOAP\. We recommend that you use either the REST API or the AWS SDKs\.

## Paying for Amazon S3<a name="PayingforStorage"></a>

Pricing for Amazon S3 is designed so that you don't have to plan for the storage requirements of your application\. Most storage providers require you to purchase a predetermined amount of storage and network transfer capacity\. In this scenario, if you exceed that capacity, your service is shut off or you are charged high overage fees\. If you do not exceed that capacity, you pay as though you used it all\. 

Amazon S3 charges you only for what you actually use, with no hidden fees and no overage charges\. This model gives you a variable\-cost service that can grow with your business while giving you the cost advantages of the AWS infrastructure\. For more information, see [Amazon S3 Pricing](https://aws.amazon.com/s3/pricing/)\.

When you sign up for AWS, your AWS account is automatically signed up for all services in AWS, including Amazon S3\. However, you are charged only for the services that you use\. If you are a new Amazon S3 customer, you can get started with Amazon S3 for free\. For more information, see [AWS free tier](http://aws.amazon.com/free)\. 

To see your bill, go to the Billing and Cost Management Dashboard in the [AWS Billing and Cost Management console](https://console.aws.amazon.com/billing/)\. To learn more about AWS account billing, see the [https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-what-is.html](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-what-is.html)\. If you have questions concerning AWS billing and AWS accounts, contact [AWS Support](http://aws.amazon.com/contact-us/)\.

## PCI DSS compliance<a name="pci-dss-compliance"></a>

Amazon S3 supports the processing, storage, and transmission of credit card data by a merchant or service provider, and has been validated as being compliant with Payment Card Industry \(PCI\) Data Security Standard \(DSS\)\. For more information about PCI DSS, including how to request a copy of the AWS PCI Compliance Package, see [PCI DSS Level 1](https://aws.amazon.com/compliance/pci-dss-level-1-faqs/)\. 