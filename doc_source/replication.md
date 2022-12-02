# Replicating objects<a name="replication"></a>

Replication enables automatic, asynchronous copying of objects across Amazon S3 buckets\. Buckets that are configured for object replication can be owned by the same AWS account or by different accounts\. You can replicate objects to a single destination bucket or to multiple destination buckets\. The destination buckets can be in different AWS Regions or within the same Region as the source bucket\.

To automatically replicate new objects as they are written to the bucket, use live replication, such as Cross\-Region Replication \(CRR\)\. To replicate existing objects to a different bucket on demand, use S3 Batch Replication\. For more information about replicating existing objects, see [When to use S3 Batch Replication](#batch-replication-scenario)\.

To enable CRR, you add a replication configuration to your source bucket\. The minimum configuration must provide the following:
+ The destination bucket or buckets where you want Amazon S3 to replicate objects 
+ An AWS Identity and Access Management \(IAM\) role that Amazon S3 can assume to replicate objects on your behalf

Additional configuration options are available\. For more information, see [Additional replication configurations](replication-additional-configs.md)\.

To get detailed metrics for S3 Replication, including replication rule count metrics, you can use Amazon S3 Storage Lens\. S3 Storage Lens is a cloud\-storage analytics feature that you can use to gain organization\-wide visibility into object\-storage usage and activity\. For more information, see [ Using S3 Storage Lens to protect your data](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-lens-data-protection?icmpid=docs_s3_user_guide_replication.html)\. For a complete list of metrics, see [ S3 Storage Lens metrics glossary](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage_lens_metrics_glossary.html?icmpid=docs_s3_user_guide_replication.html)\.

**Topics**
+ [Why use replication](#replication-scenario)
+ [When to use Cross\-Region Replication](#crr-scenario)
+ [When to use Same\-Region Replication](#srr-scenario)
+ [When to use two\-way replication \(bi\-directional replication\)](#two-way-replication-scenario)
+ [When to use S3 Batch Replication](#batch-replication-scenario)
+ [Requirements for replication](#replication-requirements)
+ [What does Amazon S3 replicate?](replication-what-is-isnot-replicated.md)
+ [Setting up replication](replication-how-setup.md)
+ [Replicating existing objects with S3 Batch Replication](s3-batch-replication-batch.md)
+ [Additional replication configurations](replication-additional-configs.md)
+ [Getting replication status information](replication-status.md)
+ [Troubleshooting replication](replication-troubleshoot.md)
+ [Additional considerations](replication-and-other-bucket-configs.md)

## Why use replication<a name="replication-scenario"></a>

Replication can help you do the following:
+ **Replicate objects while retaining metadata** – You can use replication to make copies of your objects that retain all metadata, such as the original object creation times and version IDs\. This capability is important if you must ensure that your replica is identical to the source object\.
+ **Replicate objects into different storage classes** – You can use replication to directly put objects into S3 Glacier Flexible Retrieval, S3 Glacier Deep Archive, or another storage class in the destination buckets\. You can also replicate your data to the same storage class and use lifecycle policies on the destination buckets to move your objects to a colder storage class as they age\.
+ **Maintain object copies under different ownership** – Regardless of who owns the source object, you can tell Amazon S3 to change replica ownership to the AWS account that owns the destination bucket\. This is referred to as the *owner override* option\. You can use this option to restrict access to object replicas\.
+ **Keep objects stored over multiple AWS Regions** – To ensure geographic differences in where your data is kept, you can set multiple destination buckets across different AWS Regions\. This feature might help you meet certain compliance requirements\. 
+ **Replicate objects within 15 minutes** – To replicate your data in the same AWS Region or across different Regions within a predictable time frame, you can use S3 Replication Time Control \(S3 RTC\)\. S3 RTC replicates 99\.99 percent of new objects stored in Amazon S3 within 15 minutes \(backed by a service\-level agreement\)\. For more information, see [Meeting compliance requirements using S3 Replication Time Control \(S3 RTC\)](replication-time-control.md)\.
+ **Sync buckets, replicate existing objects, and replicate previously failed or replicated objects** – To sync buckets and replicate existing objects, use Batch Replication as an on\-demand replication action\. For more information about when to use Batch Replication, see [When to use S3 Batch Replication](#batch-replication-scenario)\.
+ **Replicate objects and fail over to a bucket in another AWS Region** – To keep all metadata and objects in sync across buckets during data replication, use two\-way replication \(also known as bi\-directional replication\) rules before configuring Amazon S3 Multi\-Region Access Point failover controls\. Two\-way replication rules help ensure that when data is written to the S3 bucket that traffic fails over to, that data is then replicated back to the source bucket\.

**Note**  
S3 RTC does not apply to Batch Replication\. Batch Replication is an on\-demand replication job, and can be tracked with S3 Batch Operations\. For more information, see [Tracking job status and completion reports](batch-ops-job-status.md)\.

## When to use Cross\-Region Replication<a name="crr-scenario"></a>

S3 Cross\-Region Replication \(CRR\) is used to copy objects across Amazon S3 buckets in different AWS Regions\. CRR can help you do the following:
+ **Meet compliance requirements** – Although Amazon S3 stores your data across multiple geographically distant Availability Zones by default, compliance requirements might dictate that you store data at even greater distances\. To satisfy these requirements, use Cross\-Region Replication to replicate data between distant AWS Regions\.
+ **Minimize latency** – If your customers are in two geographic locations, you can minimize latency in accessing objects by maintaining object copies in AWS Regions that are geographically closer to your users\.
+ **Increase operational efficiency** – If you have compute clusters in two different AWS Regions that analyze the same set of objects, you might choose to maintain object copies in those Regions\.

## When to use Same\-Region Replication<a name="srr-scenario"></a>

Same\-Region Replication \(SRR\) is used to copy objects across Amazon S3 buckets in the same AWS Region\. SRR can help you do the following:
+ **Aggregate logs into a single bucket** – If you store logs in multiple buckets or across multiple accounts, you can easily replicate logs into a single, in\-Region bucket\. Doing so allows for simpler processing of logs in a single location\.
+ **Configure live replication between production and test accounts** – If you or your customers have production and test accounts that use the same data, you can replicate objects between those multiple accounts, while maintaining object metadata\.
+ **Abide by data sovereignty laws** – You might be required to store multiple copies of your data in separate AWS accounts within a certain Region\. Same\-Region Replication can help you automatically replicate critical data when compliance regulations don't allow the data to leave your country\.

## When to use two\-way replication \(bi\-directional replication\)<a name="two-way-replication-scenario"></a>
+ **Build shared datasets across multiple AWS Regions** – With replica modification sync, you can easily replicate metadata changes, such as object access control lists \(ACLs\), object tags, or object locks, on replication objects\. This two\-way replication is important if you want to keep all objects and object metadata changes in sync\. You can [enable replica modification sync](https://docs.aws.amazon.com/AmazonS3/latest/userguide/replication-for-metadata-changes.html#enabling-replication-for-metadata-changes) on a new or existing replication rule when performing two\-way replication between two or more buckets in the same or different AWS Regions\.
+ **Keep data synchronized across Regions during failover** – You can synchronize data in buckets between AWS Regions by configuring two\-way replication rules with S3 Cross\-Region Replication \(CRR\) directly from a Multi\-Region Access Point\. To make an informed decision on when to initiate failover, you can also enable S3 replication metrics to monitor the replication in Amazon CloudWatch, S3 Replication Time Control \(S3 RTC\), or from the Multi\-Region Access Point\.
+ **Make your application highly available** – Even in the event of a Regional traffic disruption, you can use two\-way replication rules to keep all metadata and objects in sync across buckets during data replication\.

## When to use S3 Batch Replication<a name="batch-replication-scenario"></a>

Batch Replication replicates existing objects to different buckets as an on\-demand option\. Unlike live replication, these jobs can be run as needed\. Batch Replication can help you do the following:
+ **Replicate existing objects** – You can use Batch Replication to replicate objects that were added to the bucket before Same\-Region Replication or Cross\-Region Replication were configured\.
+ **Replicate objects that previously failed to replicate** – You can filter a Batch Replication job to attempt to replicate objects with a replication status of **FAILED**\.
+ **Replicate objects that were already replicated** – You might be required to store multiple copies of your data in separate AWS accounts or AWS Regions\. Batch Replication can replicate existing objects to newly added destinations\.
+ **Replicate replicas of objects that were created from a replication rule** – Replication configurations create replicas of objects in destination buckets\. Replicas of objects can be replicated only with Batch Replication\.

## Requirements for replication<a name="replication-requirements"></a>

Replication requires the following:
+ The source bucket owner must have the source and destination AWS Regions enabled for their account\. The destination bucket owner must have the destination Region enabled for their account\. 

  For more information about enabling or disabling an AWS Region, see [Managing AWS Regions](https://docs.aws.amazon.com/general/latest/gr/rande-manage.html) in the *AWS General Reference*\.
+ Both source and destination buckets must have versioning enabled\. For more information about versioning, see [Using versioning in S3 buckets](Versioning.md)\.
+ Amazon S3 must have permissions to replicate objects from the source bucket to the destination bucket or buckets on your behalf\. For more information about these permissions, see [Setting up permissions](setting-repl-config-perm-overview.md)\.
+ If the owner of the source bucket doesn't own the object in the bucket, the object owner must grant the bucket owner `READ` and `READ_ACP` permissions with the object access control list \(ACL\)\. For more information, see [Access control list \(ACL\) overview](acl-overview.md)\. 
+ If the source bucket has S3 Object Lock enabled, the destination buckets must also have S3 Object Lock enabled\. 

  For more information, see [Using S3 Object Lock](object-lock.md)\. To enable replication on a bucket that has Object Lock enabled, contact [AWS Support](https://console.aws.amazon.com/support/home)\.

For more information, see [Setting up replication](replication-how-setup.md)\. 

If you are setting the replication configuration in a *cross\-account scenario*, where the source and destination buckets are owned by different AWS accounts, the following additional requirement applies:
+ The owner of the destination buckets must grant the owner of the source bucket permissions to replicate objects with a bucket policy\. For more information, see [Granting permissions when the source and destination buckets are owned by different AWS accounts](setting-repl-config-perm-overview.md#setting-repl-config-crossacct)\.
+ The destination buckets cannot be configured as Requester Pays buckets\. For more information, see [Using Requester Pays buckets for storage transfers and usage](RequesterPaysBuckets.md)\.