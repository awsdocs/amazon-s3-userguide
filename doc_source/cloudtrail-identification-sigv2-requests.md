--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Using AWS CloudTrail to identify Amazon S3 signature version 2 requests<a name="cloudtrail-identification-sigv2-requests"></a>

Amazon S3 lets you identify what API signature version was used to sign a request using an AWS CloudTrail event log\. This capability is important because support for Signature Version 2 will be turned off \(deprecated\)\. After that, Amazon S3 will no longer accept requests that use Signature Version 2, and all requests must use *Signature Version 4* signing\. 

We *strongly* recommend that you use CloudTrail to help determine whether any of your workflows are using Signature Version 2 signing\. Remediate them by upgrading your libraries and code to use Signature Version 4 instead to prevent any impact to your business\. 

For more information, see [Announcement: AWS CloudTrail for Amazon S3 adds new fields for enhanced security auditing](https://forums.aws.amazon.com/ann.jspa?annID=6551) in the AWS Discussion Forums\.

**Note**  
CloudTrail events for Amazon S3 include the signature version in the request details under the key name of '`additionalEventData`'\. To find the signature version on requests made for objects in Amazon S3 like GETs, PUTs, and DELETEs, you must enable CloudTrail data events because it is turned off by default\. 

AWS CloudTrail is the preferred method for identifying Signature Version 2 requests, if you are using Amazon S3 server access logs, see [ Using Amazon S3 access logs to identify signature version 2 requests ](using-s3-access-logs-to-identify-sigv2-requests.md)

**Topics**
+ [Athena query examples for identifying Amazon S3 signature version 2 requests](#ct-examples-identify-sigv2-requests)
+ [Partitioning signature version 2 data](#partitioning-sigv2-data)

## Athena query examples for identifying Amazon S3 signature version 2 requests<a name="ct-examples-identify-sigv2-requests"></a>

**Example — select all events that are signature version 2, and print only EventTime, S3 action, Request\_Parameters, Region, SourceIP, and UserAgent**  
In the following Athena query, replace *<s3\_cloudtrail\_events\_db\.cloudtrail\_myawsexamplebucket1\_table>* with your Athena details and increase or remove the limit as needed\.   

```
SELECT EventTime, EventName as S3_Action, requestParameters as Request_Parameters, awsregion as AWS_Region, sourceipaddress as Source_IP, useragent as User_Agent
FROM s3_cloudtrail_events_db.cloudtrail_myawsexamplebucket1_table
WHERE eventsource='s3.amazonaws.com'
AND json_extract_scalar(additionalEventData, '$.SignatureVersion')='SigV2'
LIMIT 10;
```

**Example — select all requesters that are sending signature version 2 traffic**  
   

```
SELECT useridentity.arn, Count(requestid) as RequestCount
FROM s3_cloudtrail_events_db.cloudtrail_myawsexamplebucket1_table
WHERE eventsource='s3.amazonaws.com'
    and json_extract_scalar(additionalEventData, '$.SignatureVersion')='SigV2'
Group by useridentity.arn
```

## Partitioning signature version 2 data<a name="partitioning-sigv2-data"></a>

If you have a large amount of data that you need to query, you can reduce the costs and runtime of Athena by creating a partitioned table\. 

To do this, create a new table with partitions as follows\.

```
   CREATE EXTERNAL TABLE s3_cloudtrail_events_db.cloudtrail_myawsexamplebucket1_table_partitioned(
        eventversion STRING,
        userIdentity STRUCT<
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
                    sessionIssuer:STRUCT<
                    type:STRING,
                    principalId:STRING,
                    arn:STRING,
                    accountId:STRING,
                    userName:STRING>
                >
             >,
        eventTime STRING,
        eventSource STRING,
        eventName STRING,
        awsRegion STRING,
        sourceIpAddress STRING,
        userAgent STRING,
        errorCode STRING,
        errorMessage STRING,
        requestParameters STRING,
        responseElements STRING,
        additionalEventData STRING,
        requestId STRING,
        eventId STRING,
        resources ARRAY<STRUCT<ARN:STRING,accountId: STRING,type:STRING>>, 
        eventType STRING,
        apiVersion STRING,
        readOnly STRING,
        recipientAccountId STRING,
        serviceEventDetails STRING,
        sharedEventID STRING,
        vpcEndpointId STRING
    )   
    PARTITIONED BY (region string, year string, month string, day string)
    ROW FORMAT SERDE 'com.amazon.emr.hive.serde.CloudTrailSerde'
    STORED AS INPUTFORMAT 'com.amazon.emr.cloudtrail.CloudTrailInputFormat'
    OUTPUTFORMAT 'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
    LOCATION 's3://myawsexamplebucket1/AWSLogs/111122223333/';
```

Then, create the partitions individually\. You can't get results from dates that you have not created\. 

```
ALTER TABLE s3_cloudtrail_events_db.cloudtrail_myawsexamplebucket1_table_partitioned ADD
    PARTITION (region= 'us-east-1', year= '2019', month= '02', day= '19') LOCATION 's3://myawsexamplebucket1/AWSLogs/111122223333/CloudTrail/us-east-1/2019/02/19/'
    PARTITION (region= 'us-west-1', year= '2019', month= '02', day= '19') LOCATION 's3://myawsexamplebucket1/AWSLogs/111122223333/CloudTrail/us-west-1/2019/02/19/'
    PARTITION (region= 'us-west-2', year= '2019', month= '02', day= '19') LOCATION 's3://myawsexamplebucket1/AWSLogs/111122223333/CloudTrail/us-west-2/2019/02/19/'
    PARTITION (region= 'ap-southeast-1', year= '2019', month= '02', day= '19') LOCATION 's3://myawsexamplebucket1/AWSLogs/111122223333/CloudTrail/ap-southeast-1/2019/02/19/'
    PARTITION (region= 'ap-southeast-2', year= '2019', month= '02', day= '19') LOCATION 's3://myawsexamplebucket1/AWSLogs/111122223333/CloudTrail/ap-southeast-2/2019/02/19/'
    PARTITION (region= 'ap-northeast-1', year= '2019', month= '02', day= '19') LOCATION 's3://myawsexamplebucket1/AWSLogs/111122223333/CloudTrail/ap-northeast-1/2019/02/19/'
    PARTITION (region= 'eu-west-1', year= '2019', month= '02', day= '19') LOCATION 's3://myawsexamplebucket1/AWSLogs/111122223333/CloudTrail/eu-west-1/2019/02/19/'
    PARTITION (region= 'sa-east-1', year= '2019', month= '02', day= '19') LOCATION 's3://myawsexamplebucket1/AWSLogs/111122223333/CloudTrail/sa-east-1/2019/02/19/';
```

You can then make the request based on these partitions, and you don't need to load the full bucket\. 

```
SELECT useridentity.arn,
Count(requestid) AS RequestCount
FROM s3_cloudtrail_events_db.cloudtrail_myawsexamplebucket1_table_partitioned
WHERE eventsource='s3.amazonaws.com'
AND json_extract_scalar(additionalEventData, '$.SignatureVersion')='SigV2'
AND region='us-east-1'
AND year='2019'
AND month='02'
AND day='19'
Group by useridentity.arn
```