--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Amazon S3 inventory<a name="storage-inventory"></a>

Amazon S3 inventory is one of the tools Amazon S3 provides to help manage your storage\. You can use it to audit and report on the replication and encryption status of your objects for business, compliance, and regulatory needs\. You can also simplify and speed up business workflows and big data jobs using Amazon S3 inventory, which provides a scheduled alternative to the Amazon S3 synchronous `List` API operation\.

Amazon S3 inventory provides comma\-separated values \(CSV\), [Apache optimized row columnar \(ORC\)](https://orc.apache.org/) or [Apache Parquet \(Parquet\)](https://parquet.apache.org/) output files that list your objects and their corresponding metadata on a daily or weekly basis for an S3 bucket or a shared prefix \(that is, objects that have names that begin with a common string\)\. If weekly, a report is generated every Sunday \(UTC timezone\) after the initial report\. For information about Amazon S3 inventory pricing, see [Amazon S3 Pricing](https://aws.amazon.com/s3/pricing/)\.

You can configure multiple inventory lists for a bucket\. You can configure what object metadata to include in the inventory, whether to list all object versions or only current versions, where to store the inventory list file output, and whether to generate the inventory on a daily or weekly basis\. You can also specify that the inventory list file be encrypted\.

You can query Amazon S3 inventory using standard SQL by using [Amazon Athena](https://docs.aws.amazon.com/athena/latest/ug/what-is.html), Amazon Redshift Spectrum, and other tools such as [Presto](https://prestodb.io/), [Apache Hive](https://hive.apache.org/), and [Apache Spark](https://databricks.com/spark/about/)\. It's easy to use Athena to run queries on your inventory files\. You can use Athena for Amazon S3 inventory queries in all Regions where Athena is available\. 

**Topics**
+ [Configuring Amazon S3 inventory](#storage-inventory-how-to-set-up)
+ [Amazon S3 inventory list](#storage-inventory-contents)
+ [Inventory list location](#storage-inventory-location)
+ [Inventory completion](#storage-inventory-notification)
+ [Configuring a new Amazon S3 inventory in the S3 console](configure-inventory.md)
+ [Querying Amazon S3 inventory with Amazon Athena](storage-inventory-athena-query.md)

## Configuring Amazon S3 inventory<a name="storage-inventory-how-to-set-up"></a>

This section describes how to configure an inventory, including details about the inventory source and destination buckets\.

### Source and destination buckets<a name="storage-inventory-buckets"></a>

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
+ Contains the manifest files that list all the file inventory lists that are stored in the destination bucket\. For more information, see [Inventory manifest](#storage-inventory-location-manifest)
+ Must have a bucket policy to give Amazon S3 permission to verify ownership of the bucket and permission to write files to the bucket\. 
+ Must be in the same AWS Region as the source bucket\.
+ Can be the same as the source bucket\.
+ Can be owned by a different AWS account than the account that owns the source bucket\.

### Setting up Amazon S3 inventory<a name="storage-inventory-setting-up"></a>

Amazon S3 inventory helps you manage your storage by creating lists of the objects in an S3 bucket on a defined schedule\. You can configure multiple inventory lists for a bucket\. The inventory lists are published to CSV, ORC, or Parquet files in a destination bucket\. 

The easiest way to set up an inventory is by using the AWS Management Console, but you can also use the REST API, AWS CLI, or AWS SDKs\. The console performs the first step of the following procedure for you: adding a bucket policy to the destination bucket\.

**To set up Amazon S3 inventory for an S3 bucket**

1. **Add a bucket policy for the destination bucket\.**

   You must create a bucket policy on the destination bucket to grant permissions to Amazon S3 to write objects to the bucket in the defined location\. For an example policy, see [Granting permissions for Amazon S3 inventory and Amazon S3 analytics](example-bucket-policies.md#example-bucket-policies-use-case-9)\. 

1. **Configure an inventory to list the objects in a source bucket and publish the list to a destination bucket\.**

   When you configure an inventory list for a source bucket, you specify the destination bucket where you want the list to be stored, and whether you want to generate the list daily or weekly\. You can also configure what object metadata to include and whether to list all object versions or only current versions\. 

   You can specify that the inventory list file be encrypted by using an Amazon S3 managed key \(SSE\-S3\) or an AWS Key Management Service \(AWS KMS\) customer managed customer master key \(CMK\)\. For more information about SSE\-S3 and SSE\-KMS, see [Protecting data using server\-side encryption](serv-side-encryption.md)\. If you plan to use SSE\-KMS encryption, see Step 3\.
   + For information about how to use the console to configure an inventory list, see [Configuring a new Amazon S3 inventory in the S3 console](configure-inventory.md)\.
   + To use the Amazon S3 API to configure an inventory list, use the [PUT Bucket inventory configuration](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTInventoryConfig.html) REST API or the equivalent from the AWS CLI or AWS SDKs\. 

1. **To encrypt the inventory list file with SSE\-KMS, grant Amazon S3 permission to use the CMK stored in AWS KMS\.**

   You can configure encryption for the inventory list file by using the AWS Management Console, REST API, AWS CLI, or AWS SDKs\. Whichever way you choose, you must grant Amazon S3 permission to use the AWS KMS customer managed CMK to encrypt the inventory file\. You grant Amazon S3 permission by modifying the key policy for the customer managed CMK that you want to use to encrypt the inventory file\. For more information, see the next section, [Granting Amazon S3 Permission to Use Your AWS KMS CMK for Encryption](configure-inventory.md#configure-inventory-kms-key-policy)\.

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
+ **Replication status** – Set to `PENDING`, `COMPLETED`, `FAILED`, or `REPLICA.` For more information, see [Gettinging replication status information](replication-status.md)\.
+ **Encryption status** – Set to `SSE-S3`, `SSE-C`, `SSE-KMS`, or `NOT-SSE`\. The server\-side encryption status for SSE\-S3, SSE\-KMS, and SSE with customer\-provided keys \(SSE\-C\)\. A status of `NOT-SSE` means that the object is not encrypted with server\-side encryption\. For more information, see [Protecting data using encryption](UsingEncryption.md)\.
+ **S3 Object Lock Retain until date** – The date until which the locked object cannot be deleted\. For more information, see [Using S3 Object Lock](object-lock.md)\.
+ **S3 Object Lock Mode** – Set to `Governance` or `Compliance` for objects that are locked\. For more information, see [Using S3 Object Lock](object-lock.md)\.
+ **S3 Object Lock Legal hold status ** – Set to `On` if a legal hold has been applied to an object; otherwise it is set to `Off`\. For more information, see [Using S3 Object Lock](object-lock.md)\.

We recommend that you create a lifecycle policy that deletes old inventory lists\. For more information, see [Managing your storage lifecycle](object-lifecycle-mgmt.md)\.

### Inventory consistency<a name="storage-inventory-contents-consistency"></a>

All of your objects might not appear in each inventory list\. The inventory list provides eventual consistency for PUTs of both new objects and overwrites, and DELETEs\. Inventory lists are a rolling snapshot of bucket items, which are eventually consistent \(that is, the list might not include recently added or deleted objects\)\. 

To validate the state of the object before you take action on the object, we recommend that you perform a `HEAD Object` REST API request to retrieve metadata for the object, or check the object's properties in the Amazon S3 console\. You can also check object metadata with the AWS CLI or the AWS SDKS\. For more information, see [HEAD Object](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectHEAD.html) in the *Amazon Simple Storage Service API Reference*\.

## Inventory list location<a name="storage-inventory-location"></a>

When an inventory list is published, the manifest files are published to the following location in the destination bucket\.

```
 destination-prefix/source-bucket/config-ID/YYYY-MM-DDTHH-MMZ/manifest.json
 destination-prefix/source-bucket/config-ID/YYYY-MM-DDTHH-MMZ/manifest.checksum
 destination-prefix/source-bucket/config-ID/hive/dt=YYYY-MM-DD-HH-MM/symlink.txt
```
+ *destination\-prefix* is the \(object key name\) prefix set in the inventory configuration, which can be used to group all the inventory list files in a common location within the destination bucket\.
+ *source\-bucket* is the source bucket that the inventory list is for\. It is added to prevent collisions when multiple inventory reports from different source buckets are sent to the same destination bucket\.
+ *config\-ID* is added to prevent collisions with multiple inventory reports from the same source bucket that are sent to the same destination bucket\. The *config\-ID* comes from the inventory report configuration, and is the name for the report that is defined on setup\.
+ *YYYY\-MM\-DDTHH\-MMZ* is the timestamp that consists of the start time and the date when the inventory report generation begins scanning the bucket; for example, `2016-11-06T21-32Z`\.
+ `manifest.json` is the manifest file\. 
+ `manifest.checksum` is the MD5 of the content of the `manifest.json` file\. 
+ `symlink.txt` is the Apache Hive\-compatible manifest file\. 

The inventory lists are published daily or weekly to the following location in the destination bucket\.

```
      destination-prefix/source-bucket/config-ID/example-file-name.csv.gz
      ...
      destination-prefix/source-bucket/config-ID/example-file-name-1.csv.gz
```
+ *destination\-prefix* is the \(object key name\) prefix set in the inventory configuration\. It can be used to group all the inventory list files in a common location in the destination bucket\.
+ *source\-bucket* is the source bucket that the inventory list is for\. It is added to prevent collisions when multiple inventory reports from different source buckets are sent to the same destination bucket\.
+ *example\-file\-name*`.csv.gz` is one of the CSV inventory files\. ORC inventory names end with the file name extension `.orc`, and Parquet inventory names end with the file name extension `.parquet`\.

### Inventory manifest<a name="storage-inventory-location-manifest"></a>

The manifest files `manifest.json` and `symlink.txt` describe where the inventory files are located\. Whenever a new inventory list is delivered, it is accompanied by a new set of manifest files\. These files may overwrite each other and in versioning enabled buckets will create a new versions of the manifest files\. 

Each manifest contained in the `manifest.json` file provides metadata and other basic information about an inventory\. This information includes the following:
+ Source bucket name
+ Destination bucket name
+ Version of the inventory
+ Creation timestamp in the epoch date format that consists of the start time and the date when the inventory report generation begins scanning the bucket
+ Format and schema of the inventory files
+ Actual list of the inventory files that are in the destination bucket

Whenever a `manifest.json` file is written, it is accompanied by a `manifest.checksum` file that is the MD5 of the content of `manifest.json` file\.

**Example Inventory manifest in a `manifest.json` file**  
The following examples show an inventory manifest in a `manifest.json` file for a CSV, ORC, and Parquet\-formatted inventories\.  
The following is an example of a manifest in a `manifest.json` file for a CSV\-formatted inventory\.  

```
{
    "sourceBucket": "example-source-bucket",
    "destinationBucket": "arn:aws:s3:::example-inventory-destination-bucket",
    "version": "2016-11-30",
    "creationTimestamp" : "1514944800000",
    "fileFormat": "CSV",
    "fileSchema": "Bucket, Key, VersionId, IsLatest, IsDeleteMarker, Size, LastModifiedDate, ETag, StorageClass, IsMultipartUploaded, ReplicationStatus, EncryptionStatus, ObjectLockRetainUntilDate, ObjectLockMode, ObjectLockLegalHoldStatus",
    "files": [
        {
            "key": "Inventory/example-source-bucket/2016-11-06T21-32Z/files/939c6d46-85a9-4ba8-87bd-9db705a579ce.csv.gz",
            "size": 2147483647,
            "MD5checksum": "f11166069f1990abeb9c97ace9cdfabc"
        }
    ]
}
```
The following is an example of a manifest in a `manifest.json` file for an ORC\-formatted inventory\.  

```
{
    "sourceBucket": "example-source-bucket",
    "destinationBucket": "arn:aws:s3:::example-destination-bucket",
    "version": "2016-11-30",
    "creationTimestamp" : "1514944800000",
    "fileFormat": "ORC",
    "fileSchema": "struct<bucket:string,key:string,version_id:string,is_latest:boolean,is_delete_marker:boolean,size:bigint,last_modified_date:timestamp,e_tag:string,storage_class:string,is_multipart_uploaded:boolean,replication_status:string,encryption_status:string,object_lock_retain_until_date:timestamp,object_lock_mode:string,object_lock_legal_hold_status:string>",
    "files": [
        {
            "key": "inventory/example-source-bucket/data/d794c570-95bb-4271-9128-26023c8b4900.orc",
            "size": 56291,
            "MD5checksum": "5925f4e78e1695c2d020b9f6eexample"
        }
    ]
}
```
The following is an example of a manifest in a `manifest.json` file for a Parquet\-formatted inventory\.  

```
{
    "sourceBucket": "example-source-bucket",
    "destinationBucket": "arn:aws:s3:::example-destination-bucket",
    "version": "2016-11-30",
    "creationTimestamp" : "1514944800000",
    "fileFormat": "Parquet",
    "fileSchema": "message s3.inventory { required binary bucket (UTF8); required binary key (UTF8); optional binary version_id (UTF8); optional boolean is_latest; optional boolean is_delete_marker;  optional int64 size;  optional int64 last_modified_date (TIMESTAMP_MILLIS);  optional binary e_tag (UTF8);  optional binary storage_class (UTF8);  optional boolean is_multipart_uploaded;  optional binary replication_status (UTF8);  optional binary encryption_status (UTF8);}"
  "files": [
        {
           "key": "inventory/example-source-bucket/data/d754c470-85bb-4255-9218-47023c8b4910.parquet",
            "size": 56291,
            "MD5checksum": "5825f2e18e1695c2d030b9f6eexample" 
        }
    ]
}
```
The `symlink.txt` file is an Apache Hive\-compatible manifest file that allows Hive to automatically discover inventory files and their associated data files\. The Hive\-compatible manifest works with the Hive\-compatible services Athena and Amazon Redshift Spectrum\. It also works with Hive\-compatible applications, including [Presto](https://prestodb.io/), [Apache Hive](https://hive.apache.org/), [Apache Spark](https://databricks.com/spark/about/), and many others\.  
The `symlink.txt` Apache Hive\-compatible manifest file does not currently work with AWS Glue\.  
Reading `symlink.txt` with [Apache Hive](https://hive.apache.org/) and [Apache Spark](https://databricks.com/spark/about/) is not supported for ORC and Parquet\-formatted inventory files\. 

## Inventory completion<a name="storage-inventory-notification"></a>

You can set up an Amazon S3 event notification to receive notice when the manifest checksum file is created, which indicates that an inventory list has been added to the destination bucket\. The manifest is an up\-to\-date list of all the inventory lists at the destination location\.

Amazon S3 can publish events to an Amazon Simple Notification Service \(Amazon SNS\) topic, an Amazon Simple Queue Service \(Amazon SQS\) queue, or an AWS Lambda function\. For more information, see [Configuring Amazon S3 event notifications](NotificationHowTo.md)\.

The following notification configuration defines that all `manifest.checksum` files newly added to the destination bucket are processed by the AWS Lambda `cloud-function-list-write`\.

```
<NotificationConfiguration>
  <QueueConfiguration>
      <Id>1</Id>
      <Filter>
          <S3Key>
              <FilterRule>
                  <Name>prefix</Name>
                  <Value>destination-prefix/source-bucket</Value>
              </FilterRule>
              <FilterRule>
                  <Name>suffix</Name>
                  <Value>checksum</Value>
              </FilterRule>
          </S3Key>
     </Filter>
     <Cloudcode>arn:aws:lambda:us-west-2:222233334444:cloud-function-list-write</Cloudcode>
     <Event>s3:ObjectCreated:*</Event>
  </QueueConfiguration>
  </NotificationConfiguration>
```

For more information, see [Using AWS Lambda with Amazon S3](https://docs.aws.amazon.com/lambda/latest/dg/with-s3.html) in the *AWS Lambda Developer Guide*\.