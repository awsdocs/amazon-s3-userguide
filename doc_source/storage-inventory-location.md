# Locating your inventory list<a name="storage-inventory-location"></a>

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
      destination-prefix/source-bucket/config-ID/data/example-file-name.csv.gz
      ...
      destination-prefix/source-bucket/config-ID/data/example-file-name-1.csv.gz
```
+ *destination\-prefix* is the \(object key name\) prefix set in the inventory configuration\. It can be used to group all the inventory list files in a common location in the destination bucket\.
+ *source\-bucket* is the source bucket that the inventory list is for\. It is added to prevent collisions when multiple inventory reports from different source buckets are sent to the same destination bucket\.
+ *example\-file\-name*`.csv.gz` is one of the CSV inventory files\. ORC inventory names end with the file name extension `.orc`, and Parquet inventory names end with the file name extension `.parquet`\.

## Inventory manifest<a name="storage-inventory-location-manifest"></a>

The manifest files `manifest.json` and `symlink.txt` describe where the inventory files are located\. Whenever a new inventory list is delivered, it is accompanied by a new set of manifest files\. These files may overwrite each other\. In versioning\-enabled buckets, Amazon S3 creates new versions of the manifest files\. 

Each manifest contained in the `manifest.json` file provides metadata and other basic information about an inventory\. This information includes the following:
+ Source bucket name
+ Destination bucket name
+ Version of the inventory
+ Creation timestamp in the epoch date format that consists of the start time and the date when the inventory report generation begins scanning the bucket
+ Format and schema of the inventory files
+ Actual list of the inventory files that are in the destination bucket

Whenever a `manifest.json` file is written, it is accompanied by a `manifest.checksum` file that is the MD5 of the content of `manifest.json` file\.

**Example Inventory manifest in a manifest\.json file**  
The following examples show an inventory manifest in a `manifest.json` file for a CSV, ORC, and Parquet\-formatted inventories\.  
The following is an example of a manifest in a `manifest.json` file for a CSV\-formatted inventory\.  

```
{
    "sourceBucket": "example-source-bucket",
    "destinationBucket": "arn:aws:s3:::example-inventory-destination-bucket",
    "version": "2016-11-30",
    "creationTimestamp" : "1514944800000",
    "fileFormat": "CSV",
    "fileSchema": "Bucket, Key, VersionId, IsLatest, IsDeleteMarker, Size, LastModifiedDate, ETag, StorageClass, IsMultipartUploaded, ReplicationStatus, EncryptionStatus, ObjectLockRetainUntilDate, ObjectLockMode, ObjectLockLegalHoldStatus, IntelligentTieringAccessTier, BucketKeyStatus, ChecksumAlgorithm",
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
    "fileSchema": "struct<bucket:string,key:string,version_id:string,is_latest:boolean,is_delete_marker:boolean,size:bigint,last_modified_date:timestamp,e_tag:string,storage_class:string,is_multipart_uploaded:boolean,replication_status:string,encryption_status:string,object_lock_retain_until_date:timestamp,object_lock_mode:string,object_lock_legal_hold_status:string,intelligent_tiering_access_tier:string,bucket_key_status:string,checksum_algorithm:string>",
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
    "fileSchema": "message s3.inventory { required binary bucket (UTF8); required binary key (UTF8); optional binary version_id (UTF8); optional boolean is_latest; optional boolean is_delete_marker; optional int64 size; optional int64 last_modified_date (TIMESTAMP_MILLIS); optional binary e_tag (UTF8); optional binary storage_class (UTF8); optional boolean is_multipart_uploaded; optional binary replication_status (UTF8); optional binary encryption_status (UTF8); optional int64 object_lock_retain_until_date (TIMESTAMP_MILLIS); optional binary object_lock_mode (UTF8); optional binary object_lock_legal_hold_status (UTF8); optional binary intelligent_tiering_access_tier (UTF8); optional binary bucket_key_status (UTF8); optional binary checksum_algorithm (UTF8); }",
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