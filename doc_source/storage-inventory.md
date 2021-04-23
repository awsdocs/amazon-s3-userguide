# Amazon S3 inventory<a name="storage-inventory"></a>

Amazon S3 inventory is one of the tools Amazon S3 provides to help manage your storage\. You can use it to audit and report on the replication and encryption status of your objects for business, compliance, and regulatory needs\. You can also simplify and speed up business workflows and big data jobs using Amazon S3 inventory, which provides a scheduled alternative to the Amazon S3 synchronous `List` API operation\.

Amazon S3 inventory provides comma\-separated values \(CSV\), [Apache optimized row columnar \(ORC\)](https://orc.apache.org/) or [Apache Parquet \(Parquet\)](https://parquet.apache.org/) output files that list your objects and their corresponding metadata on a daily or weekly basis for an S3 bucket or a shared prefix \(that is, objects that have names that begin with a common string\)\. If weekly, a report is generated every Sunday \(UTC timezone\) after the initial report\. For information about Amazon S3 inventory pricing, see [Amazon S3 Pricing](https://aws.amazon.com/s3/pricing/)\.

You can configure multiple inventory lists for a bucket\. You can configure what object metadata to include in the inventory, whether to list all object versions or only current versions, where to store the inventory list file output, and whether to generate the inventory on a daily or weekly basis\. You can also specify that the inventory list file be encrypted\.

You can query Amazon S3 inventory using standard SQL by using [Amazon Athena](https://docs.aws.amazon.com/athena/latest/ug/what-is.html), Amazon Redshift Spectrum, and other tools such as [Presto](https://prestodb.io/), [Apache Hive](https://hive.apache.org/), and [Apache Spark](https://databricks.com/spark/about/)\. It's easy to use Athena to run queries on your inventory files\. You can use Athena for Amazon S3 inventory queries in all Regions where Athena is available\. 

## Source and destination buckets<a name="storage-inventory-buckets"></a>

The bucket that the inventory lists the objects for is called the *source bucket*\. The bucket where the inventory list file is stored is called the *destination bucket*\. 

**Source Bucket**

The inventory lists the objects that are stored in the source bucket\. You can get inventory lists for an entire bucket or filtered by \(object key name\) prefix\.

The source bucket:
+ Contains the objects that are listed in the inventory\.
+ Contains the configuration for the inventory\.

**Destination Bucket**

Amazon S3 inventory list files are written to the destination bucket\. To group all the inventory list files in a common location in the destination bucket, you can specify a destination prefix \(object key name\) in the inventory configuration\.

The destination bucket:
+ Contains the inventory file lists\. 
+ Contains the manifest files that list all the file inventory lists that are stored in the destination bucket\. For more information, see [Inventory manifest](storage-inventory-location.md#storage-inventory-location-manifest)
+ Must have a bucket policy to give Amazon S3 permission to verify ownership of the bucket and permission to write files to the bucket\. 
+ Must be in the same AWS Region as the source bucket\.
+ Can be the same as the source bucket\.
+ Can be owned by a different AWS account than the account that owns the source bucket\.

## Amazon S3 inventory list<a name="storage-inventory-contents"></a>

An inventory list file contains a list of the objects in the source bucket and metadata for each object\. The inventory lists are stored in the destination bucket as a CSV file compressed with GZIP, as an Apache optimized row columnar \(ORC\) file compressed with ZLIB, or as an Apache Parquet \(Parquet\) file compressed with Snappy\. 

The inventory list contains a list of the objects in an S3 bucket and the following metadata for each listed object: 
+ **Bucket name** – The name of the bucket that the inventory is for\.
+ **Key name** – Object key name \(or key\) that uniquely identifies the object in the bucket\. When using the CSV file format, the key name is URL\-encoded and must be decoded before you can use it\.
+ **Version ID** – Object version ID\. When you enable versioning on a bucket, Amazon S3 assigns a version number to objects that are added to the bucket\. For more information, see [Using versioning in S3 buckets](Versioning.md)\. \(This field is not included if the list is only for the current version of objects\.\)
+ **IsLatest** – Set to `True` if the object is the current version of the object\. \(This field is not included if the list is only for the current version of objects\.\)
+ **Size** – Object size in bytes\.
+ **Last modified date** – Object creation date or the last modified date, whichever is the latest\.
+ **ETag** – The entity tag is a hash of the object\. The ETag reflects changes only to the contents of an object, not its metadata\. The ETag may or may not be an MD5 digest of the object data\. Whether it is depends on how the object was created and how it is encrypted\.
+ **Storage class** – Storage class used for storing the object\. For more information, see [Using Amazon S3 storage classes](storage-class-intro.md)\.
+ **Intelligent\-Tiering access tier** – Access tier \(frequent or infrequent\) of the object if stored in Intelligent\-Tiering\. For more information, see [Storage class for automatically optimizing data with changing or unknown access patterns](storage-class-intro.md#sc-dynamic-data-access)\.
+ **Multipart upload flag** – Set to `True` if the object was uploaded as a multipart upload\. For more information, see [Uploading and copying objects using multipart upload](mpuoverview.md)\.
+ **Delete marker** – Set to `True`, if the object is a delete marker\. For more information, see [Using versioning in S3 buckets](Versioning.md)\. \(This field is automatically added to your report if you've configured the report to include all versions of your objects\)\.
+ **Replication status** – Set to `PENDING`, `COMPLETED`, `FAILED`, or `REPLICA.` For more information, see [Getting replication status information](replication-status.md)\.
+ **Encryption status** – Set to `SSE-S3`, `SSE-C`, `SSE-KMS`, or `NOT-SSE`\. The server\-side encryption status for SSE\-S3, SSE\-KMS, and SSE with customer\-provided keys \(SSE\-C\)\. A status of `NOT-SSE` means that the object is not encrypted with server\-side encryption\. For more information, see [Protecting data using encryption](UsingEncryption.md)\.
+ **S3 Object Lock Retain until date** – The date until which the locked object cannot be deleted\. For more information, see [Using S3 Object Lock](object-lock.md)\.
+ **S3 Object Lock Mode** – Set to `Governance` or `Compliance` for objects that are locked\. For more information, see [Using S3 Object Lock](object-lock.md)\.
+ **S3 Object Lock Legal hold status ** – Set to `On` if a legal hold has been applied to an object; otherwise it is set to `Off`\. For more information, see [Using S3 Object Lock](object-lock.md)\.

We recommend that you create a lifecycle policy that deletes old inventory lists\. For more information, see [Managing your storage lifecycle](object-lifecycle-mgmt.md)\.

### Inventory consistency<a name="storage-inventory-contents-consistency"></a>

All of your objects might not appear in each inventory list\. The inventory list provides eventual consistency for PUTs of both new objects and overwrites, and DELETEs\. Inventory lists are a rolling snapshot of bucket items, which are eventually consistent \(that is, the list might not include recently added or deleted objects\)\. 

To validate the state of the object before you take action on the object, we recommend that you perform a `HEAD Object` REST API request to retrieve metadata for the object, or check the object's properties in the Amazon S3 console\. You can also check object metadata with the AWS CLI or the AWS SDKS\. For more information, see [HEAD Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectHEAD.html) in the *Amazon Simple Storage Service API Reference*\.

For more information about working with S3 inventory, see the topics below\.

**Topics**
+ [Source and destination buckets](#storage-inventory-buckets)
+ [Amazon S3 inventory list](#storage-inventory-contents)
+ [Configuring Amazon S3 inventory](configure-inventory.md)
+ [Setting up Amazon S3 event notifications for inventory completion](storage-inventory-notification.md)
+ [Locating your inventory list](storage-inventory-location.md)
+ [Querying Amazon S3 inventory with Amazon Athena](storage-inventory-athena-query.md)