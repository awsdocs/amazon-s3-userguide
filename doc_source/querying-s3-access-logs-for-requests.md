--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Querying Amazon S3 access logs for requests using Amazon Athena<a name="querying-s3-access-logs-for-requests"></a>

You can identify Amazon S3 requests with Amazon S3 access logs using Amazon Athena\. 

Amazon S3 stores server access logs as objects in an S3 bucket\. It is often easier to use a tool that can analyze the logs in Amazon S3\. Athena supports analysis of S3 objects and can be used to query Amazon S3 access logs\.

**Example**  
The following example shows how you can query Amazon S3 server access logs in Amazon Athena\.   
To specify an Amazon S3 location in an Athena query, you need to format the *target* bucket name and *target* prefix where your logs are delivered as an S3 URI, as follows: `s3://DOC-EXAMPLE-BUCKET1-logs/prefix/` 

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. In the Query Editor, run a command similar to the following:

   ```
   create database s3_access_logs_db
   ```
**Note**  
It's a best practice to create the database in the same AWS Region as your S3 bucket\. 

1. In the Query Editor, run a command similar to the following to create a table schema in the database that you created in step 2\. The `STRING` and `BIGINT` data type values are the access log properties\. You can query these properties in Athena\. For `LOCATION`, enter the S3 bucket and prefix path as noted earlier\.

   ```
   CREATE EXTERNAL TABLE `s3_access_logs_db.mybucket_logs`(
     `bucketowner` STRING, 
     `bucket_name` STRING, 
     `requestdatetime` STRING, 
     `remoteip` STRING, 
     `requester` STRING, 
     `requestid` STRING, 
     `operation` STRING, 
     `key` STRING, 
     `request_uri` STRING, 
     `httpstatus` STRING, 
     `errorcode` STRING, 
     `bytessent` BIGINT, 
     `objectsize` BIGINT, 
     `totaltime` STRING, 
     `turnaroundtime` STRING, 
     `referrer` STRING, 
     `useragent` STRING, 
     `versionid` STRING, 
     `hostid` STRING, 
     `sigv` STRING, 
     `ciphersuite` STRING, 
     `authtype` STRING, 
     `endpoint` STRING, 
     `tlsversion` STRING)
   ROW FORMAT SERDE 
     'org.apache.hadoop.hive.serde2.RegexSerDe' 
   WITH SERDEPROPERTIES ( 
     'input.regex'='([^ ]*) ([^ ]*) \\[(.*?)\\] ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) (\"[^\"]*\"|-) (-|[0-9]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) (\"[^\"]*\"|-) ([^ ]*)(?: ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*) ([^ ]*))?.*$') 
   STORED AS INPUTFORMAT 
     'org.apache.hadoop.mapred.TextInputFormat' 
   OUTPUTFORMAT 
     'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
   LOCATION
     's3://awsexamplebucket1-logs/prefix/'
   ```

1. In the navigation pane, under **Database**, choose your database\.

1. Under **Tables**, choose **Preview table** next to your table name\.

   In the **Results** pane, you should see data from the server access logs, such as `bucketowner`, `bucket`, `requestdatetime`, and so on\. This means that you successfully created the Athena table\. You can now query the Amazon S3 server access logs\.

**Example — show who deleted an object and when \(timestamp, IP address, and IAM user\)**  

```
SELECT RequestDateTime, RemoteIP, Requester, Key 
FROM s3_access_logs_db.mybucket_logs 
WHERE key = 'images/picture.jpg' AND operation like '%DELETE%';
```

**Example — show all operations executed by an IAM user**  

```
SELECT * 
FROM s3_access_logs_db.mybucket_logs 
WHERE requester='arn:aws:iam::123456789123:user/user_name';
```

**Example — show all operations that were performed on an object in a specific time period**  

```
SELECT *
FROM s3_access_logs_db.mybucket_logs
WHERE Key='prefix/images/picture.jpg' 
    AND parse_datetime(RequestDateTime,'dd/MMM/yyyy:HH:mm:ss Z')
    BETWEEN parse_datetime('2017-02-18:07:00:00','yyyy-MM-dd:HH:mm:ss')
    AND parse_datetime('2017-02-18:08:00:00','yyyy-MM-dd:HH:mm:ss');
```

**Example — show how much data was transferred by a specific IP address in a specific time period**  

```
SELECT SUM(bytessent) AS uploadTotal,
      SUM(objectsize) AS downloadTotal,
      SUM(bytessent + objectsize) AS Total
FROM s3_access_logs_db.mybucket_logs
WHERE RemoteIP='1.2.3.4'
AND parse_datetime(RequestDateTime,'dd/MMM/yyyy:HH:mm:ss Z')
BETWEEN parse_datetime('2017-06-01','yyyy-MM-dd')
AND parse_datetime('2017-07-01','yyyy-MM-dd');
```