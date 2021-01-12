--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Identifying requests made to Amazon S3 in a CloudTrail log<a name="identify-S3-requests-using-in-CTlog"></a>

Events logged by CloudTrail are stored as compressed, GZipped JSON objects in your S3 bucket\. To efficiently find requests, you should use a service like Amazon Athena to index and query the CloudTrail logs\. For more information about CloudTrail and Athena, see [Querying AWS CloudTrail Logs](https://docs.aws.amazon.com/athena/latest/ug/cloudtrail-logs.html)\.

**Topics**
+ [Using Athena with CloudTrail logs](#using-athena)

## Using Athena with CloudTrail logs<a name="using-athena"></a>

After you set up CloudTrail to deliver events to a bucket, you should start to see objects go to your destination bucket on the Amazon S3 console\. These are formatted as follows: `s3://<myawsexamplebucket1>/AWSLogs/<111122223333>/CloudTrail/<Region>/<yyyy>/<mm>/<dd>` 

**Example — use Athena to query CloudTrail event logs for specific requests**  

Locate your CloudTrail event logs:

`s3://myawsexamplebucket1/AWSLogs/111122223333/CloudTrail/us-east-2/2019/04/14`

With CloudTrail event logs, you can now create an Athena database and table to query them as follows:

1. Open the Athena console at [https://console\.aws\.amazon\.com/athena/](https://console.aws.amazon.com/athena/home)\.

1. Change the AWS Region to be the same as your CloudTrail destination S3 bucket\.

1. In the query window, create an Athena database for your CloudTrail events\.

   ```
   CREATE DATABASE s3_cloudtrail_events_db
   ```

1. Use the following query to create a table for all of your CloudTrail events in the bucket\. Be sure to change the bucket name from *<CloudTrail\_myawsexamplebucket1>* to your bucket's name\. Also provide the *AWS\_account\_ID* CloudTrail that is used in your bucket\. 

   ```
   CREATE EXTERNAL TABLE s3_cloudtrail_events_db.cloudtrail_myawsexamplebucket1_table(
           eventversion STRING,
           useridentity STRUCT<
               type:STRING,
               principalid:STRING,
               arn:STRING,
               accountid:STRING,
               invokedby:STRING,
               accesskeyid:STRING,
               userName:STRING,
               sessioncontext:STRUCT<
                   attributes:STRUCT<
                           mfaauthenticated:STRING,
                           creationdate:STRING>,
                           sessionissuer:STRUCT<  
                           type:STRING,
                           principalId:STRING,
                           arn:STRING, 
                           accountId:STRING,
                           userName:STRING>
                   >
                >,
           eventtime STRING,
           eventsource STRING,
           eventname STRING,
           awsregion STRING,
           sourceipaddress STRING,
           useragent STRING,
           errorcode STRING,
           errormessage STRING,
           requestparameters STRING,
           responseelements STRING,
           additionaleventdata STRING,
           requestid STRING,
           eventid STRING,
           resources ARRAY<STRUCT<
               ARN:STRING,
               accountId:STRING,
               type:STRING>>,
           eventtype STRING,
           apiversion STRING,
           readonly STRING,
           recipientaccountid STRING,
           serviceeventdetails STRING,
           sharedeventid STRING,
           vpcendpointid STRING
       )
       ROW FORMAT SERDE 'com.amazon.emr.hive.serde.CloudTrailSerde'
       STORED AS INPUTFORMAT 'com.amazon.emr.cloudtrail.CloudTrailInputFormat'
       OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
       LOCATION 's3://<myawsexamplebucket1>/AWSLogs/<111122223333>/';
   ```

1. Test Athena to ensure that the query works\.

   ```
   SELECT * FROM s3_cloudtrail_events_db.cloudtrail_myawsexamplebucket1_table
   WHERE eventsource='s3.amazonaws.com'
   LIMIT 2;
   ```