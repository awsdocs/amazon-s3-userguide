# Querying Amazon S3 Inventory with Amazon Athena<a name="storage-inventory-athena-query"></a>

You can query Amazon S3 Inventory using standard SQL by using Amazon Athena in all Regions where Athena is available\. To check for AWS Region availability, see the [AWS Region Table](https://aws.amazon.com/about-aws/global-infrastructure/regional-product-services/)\. 

Athena can query Amazon S3 Inventory files in ORC, Parquet, or CSV format\. When you use Athena to query inventory, we recommend that you use ORC\-formatted or Parquet\-formatted inventory files\. ORC and Parquet formats provide faster query performance and lower query costs\. ORC and Parquet are self\-describing type\-aware columnar file formats designed for [Apache Hadoop](http://hadoop.apache.org/)\. The columnar format lets the reader read, decompress, and process only the columns that are required for the current query\. The ORC and Parquet formats for Amazon S3 Inventory are available in all AWS Regions\.

**To get started using Athena to query Amazon S3 Inventory**

1. Create an Athena table\. For information about creating a table, see [Creating Tables in Amazon Athena](https://docs.aws.amazon.com/athena/latest/ug/creating-tables.html) in the *Amazon Athena User Guide*\.

   The following sample query includes all optional fields in an ORC\-formatted inventory report\. Drop any optional field that you did not choose for your inventory so that the query corresponds to the fields chosen for your inventory\. Also, you must use your bucket name and location to your inventory destination path\. Replace the following bucket name and inventory location as appropriate for your configuration: *`s3://destination-prefix/DOC-EXAMPLE-BUCKET/config-ID/hive/`*\. You should also replace the initial date under `projection.dt.range` to the first day with data\.

   ```
   CREATE EXTERNAL TABLE your_table_name(
            bucket string,
            key string,
            version_id string,
            is_latest boolean,
            is_delete_marker boolean,
            size bigint,
            last_modified_date bigint,
            e_tag string,
            storage_class string,
            is_multipart_uploaded boolean,
            replication_status string,
            encryption_status string,
            object_lock_retain_until_date bigint,
            object_lock_mode string,
            object_lock_legal_hold_status string,
            intelligent_tiering_access_tier string,
            bucket_key_status string,
            checksum_algorithm string
   ) PARTITIONED BY (
           dt string
   )
   ROW FORMAT SERDE 'org.apache.hadoop.hive.ql.io.orc.OrcSerde'
     STORED AS INPUTFORMAT 'org.apache.hadoop.hive.ql.io.SymlinkTextInputFormat'
     OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.IgnoreKeyTextOutputFormat'
     LOCATION 's3://destination-prefix/source-bucket/config-ID/hive/'
     TBLPROPERTIES (
       "projection.enabled" = "true",
       "projection.dt.type" = "date",
       "projection.dt.format" = "yyyy-MM-dd-HH-mm",
       "projection.dt.range" = "2022-01-01-00-00,NOW",
       "projection.dt.interval" = "1",
       "projection.dt.interval.unit" = "DAYS"
     );
   ```

   When using Athena to query a Parquet\-formatted inventory report, use the following Parquet SerDe in place of the ORC SerDe in the `ROW FORMAT SERDE` statement\.

   ```
   ROW FORMAT SERDE 'org.apache.hadoop.hive.ql.io.parquet.serde.ParquetHiveSerDe'
   ```

   When using Athena to query a CSV\-formatted inventory report, use the following template\.

   ```
   CREATE EXTERNAL TABLE your_table_name(
            bucket string,
            key string,
            version_id string,
            is_latest boolean,
            is_delete_marker boolean,
            size string,
            last_modified_date string,
            e_tag string,
            storage_class string,
            is_multipart_uploaded boolean,
            replication_status string,
            encryption_status string,
            object_lock_retain_until_date string,
            object_lock_mode string,
            object_lock_legal_hold_status string,
            intelligent_tiering_access_tier string,
            bucket_key_status string,
            checksum_algorithm string
   ) PARTITIONED BY (
           dt string
   )
   ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.OpenCSVSerde'
     STORED AS INPUTFORMAT 'org.apache.hadoop.hive.ql.io.SymlinkTextInputFormat'
     OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.IgnoreKeyTextOutputFormat'
     LOCATION 's3://destination-prefix/source-bucket/config-ID/hive/'
     TBLPROPERTIES (
       "projection.enabled" = "true",
       "projection.dt.type" = "date",
       "projection.dt.format" = "yyyy-MM-dd-HH-mm",
       "projection.dt.range" = "2022-01-01-00-00,NOW",
       "projection.dt.interval" = "1",
       "projection.dt.interval.unit" = "DAYS"
     );
   ```

1. After performing this step, you can run ad hoc queries on your inventory, as shown in the following examples\. 

   ```
   # Get list of latest inventory report dates available
   SELECT DISTINCT dt FROM your_table_name ORDER BY 1 DESC limit 10;
             
   # Get encryption status for a provided report date.
   SELECT encryption_status, count(*) FROM your_table_name WHERE dt = 'YYYY-MM-DD-HH-MM' GROUP BY encryption_status;
             
   # Get encryption status for report dates in the provided range.
   SELECT dt, encryption_status, count(*) FROM your_table_name 
   WHERE dt > 'YYYY-MM-DD-HH-MM' AND dt < 'YYYY-MM-DD-HH-MM' GROUP BY dt, encryption_status;
   ```

For more information about using Athena, see [Amazon Athena User Guide](https://docs.aws.amazon.com/athena/latest/ug/)\.

The following are the REST operations used for Amazon S3 Inventory\.
+  [ DELETE Bucket Inventory ](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketDELETEInventoryConfiguration.html) 
+  [ GET Bucket Inventory](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETInventoryConfig.html) 
+  [ List Bucket Inventory](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketListInventoryConfigs.html) 
+  [ PUT Bucket Inventory](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTInventoryConfig.html) 