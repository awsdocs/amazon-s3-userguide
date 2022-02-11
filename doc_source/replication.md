# Replicating objects<a name="replication"></a>

Replication enables automatic, asynchronous copying of objects across Amazon S3 buckets\. Buckets that are configured for object replication can be owned by the same AWS account or by different accounts\. You can replicate objects to a single destination bucket or to multiple destination buckets\. The destination buckets can be in different AWS Regions or within the same Region as the source bucket\. 

To automatically replicate new objects as they are written to the bucket use live replication, such as Same\-Region Replication \(SRR\) or Cross\-Region Replication \(CRR\)\. To replicate existing objects to a different bucket on demand, use S3 Batch Replication\. For more information about replicating existing objects, see [When to use S3 Batch Replication](#batch-replication-scenario)\.

To enable SRR or CRR, you add a replication configuration to your source bucket\. The minimum configuration must provide the following:
+ The destination bucket or buckets where you want Amazon S3 to replicate objects 
+ An AWS Identity and Access Management \(IAM\) role that Amazon S3 can assume to replicate objects on your behalf

Additional configuration options are available\. For more information, see [Additional replication configurations](replication-additional-configs.md)\.

**Topics**
+ [Why use replication](#replication-scenario)
+ [When to use Cross\-Region Replication](#crr-scenario)
+ [When to use Same\-Region Replication](#srr-scenario)
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

## When to use S3 Batch Replication<a name="batch-replication-scenario"></a>

Batch Replication replicates existing objects to different buckets as an on\-demand option\. Unlike live replication, these jobs can be ran as needed\. Batch Replication can help you do the following:
+ **Replicate existing objects** – You can use Batch Replication to replicate objects that were added to the bucket before Same\-Region Replication or Cross\-Region Replication were configured\.
+ **Replicate objects that previously failed to replicate** – You can filter a Batch Replication job to attempt to replicate objects with a replication status of **FAILED**\.
+ **Replicate objects that were already replicated** – You might be required to store multiple copies of your data in separate AWS accounts or AWS Regions\. Batch Replication can replicate existing objects to newly added destinations\.
+ **Replicate replicas of objects that were created from a replication rule** – Replication configurations create replicas of objects in destination buckets\. Replicas of objects can be replicated only with Batch Replication\.

## Requirements for replication<a name="replication-requirements"></a>

Replication requires the following:
+ The source bucket owner must have the source and destination AWS Regions enabled for their account\. The destination bucket owner must have the destination Region enabled for their account\. 

  For more information about enabling or disabling an AWS Region, see [AWS Service Endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html) in the *AWS General Reference*\.
+ Both source and destination buckets must have versioning enabled\. For more information about versioning, see [Using versioning in S3 buckets](Versioning.md)\.
+ Amazon S3 must have permissions to replicate objects from the source bucket to the destination bucket or buckets on your behalf\. For more information about these permissions, see [Setting up permissions](setting-repl-config-perm-overview.md)\.
+ If the owner of the source bucket doesn't own the object in the bucket, the object owner must grant the bucket owner `READ` and `READ_ACP` permissions with the object access control list \(ACL\)\. For more information, see [Access control list \(ACL\) overview](acl-overview.md)\. 
+ If the source bucket has S3 Object Lock enabled, the destination buckets must also have S3 Object Lock enabled\. 

  For more information, see [Using S3 Object Lock](object-lock.md)\. To enable replication on a bucket that has Object Lock enabled, contact [AWS Support](https://console.aws.amazon.com/support/home)\.

For more information, see [Setting up replication](replication-how-setup.md)\. 

If you are setting the replication configuration in a *cross\-account scenario*, where source and destination buckets are owned by different AWS accounts, the following additional requirement applies:
+ The owner of the destination buckets must grant the owner of the source bucket permissions to replicate objects with a bucket policy\. For more information, see [Granting permissions when the source and destination buckets are owned by different AWS accounts](setting-repl-config-perm-overview.md#setting-repl-config-crossacct)\.
+ The destination buckets cannot be configured as Requester Pays buckets\. For more information, see [Using Requester Pays buckets for storage transfers and usage](RequesterPaysBuckets.md)\.