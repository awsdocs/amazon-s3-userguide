--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Using AWS CloudTrail to identify access to Amazon S3 objects<a name="cloudtrail-identification-object-access"></a>

You can use your AWS CloudTrail event log to identify Amazon S3 object access requests for data events such as GetObject, DeleteObject, and PutObject, and discover further information about those requests\.

The following example shows how to get all PUT object requests for Amazon S3 from the AWS CloudTrail event log\. 

**Topics**
+ [Athena query example for identifying Amazon S3 object access requests](#ct-examples-identify-object-access-requests)

## Athena query example for identifying Amazon S3 object access requests<a name="ct-examples-identify-object-access-requests"></a>

In the following Athena query examples, replace *<s3\_cloudtrail\_events\_db\.cloudtrail\_myawsexamplebucket1\_table>* with your Athena details, and modify the date range as needed\. 

**Example — select all events that have PUT object access requests, and print only EventTime, EventSource, SourceIP, UserAgent, BucketName, object, and UserARN**  

```
SELECT
  eventTime, 
  eventName, 
  eventSource, 
  sourceIpAddress, 
  userAgent, 
  json_extract_scalar(requestParameters, '$.bucketName') as bucketName, 
  json_extract_scalar(requestParameters, '$.key') as object,
  userIdentity.arn as userArn
FROM
  s3_cloudtrail_events_db.cloudtrail_myawsexamplebucket_table
WHERE
  eventName = 'PutObject'
  AND eventTime BETWEEN '2019-07-05T00:00:00Z' and '2019-07-06T00:00:00Z'
```

**Example — select all events that have GET object access requests, and print only EventTime, EventSource, SourceIP, UserAgent, BucketName, object, and UserARN**  

```
SELECT
  eventTime, 
  eventName, 
  eventSource, 
  sourceIpAddress, 
  userAgent, 
  json_extract_scalar(requestParameters, '$.bucketName') as bucketName, 
  json_extract_scalar(requestParameters, '$.key') as object,
  userIdentity.arn as userArn
FROM
  s3_cloudtrail_events_db.cloudtrail_myawsexamplebucket_table
WHERE
  eventName = 'GetObject'
  AND eventTime BETWEEN '2019-07-05T00:00:00Z' and '2019-07-06T00:00:00Z'
```

**Example — select all anonymous requester events to a bucket in a certain period and print only EventTime, EventSource, SourceIP, UserAgent, BucketName, UserIdentity, and UserARN**  

```
SELECT
  eventTime, 
  eventName, 
  eventSource, 
  sourceIpAddress, 
  userAgent, 
  json_extract_scalar(requestParameters, '$.bucketName') as bucketName, 
  userIdentity.arn as userArn,
  userIdentity.principalId 
FROM
  s3_cloudtrail_events_db.cloudtrail_myawsexamplebucket_table
WHERE
  userIdentity.principalId='ANONYMOUS_PRINCIPAL'
  AND eventTime BETWEEN '2019-07-05T00:00:00Z' and '2019-07-06T00:00:00Z'
```

**Note**  
These query examples may also be useful for security monitoring\. You can review the results for `PutObject` or `GetObject` calls from unexpected or unauthorized IP addresses/requesters and for identifying any anonymous requests to your buckets\.
 This query only retrieves information from the time at which logging was enabled\. 

If you are using Amazon S3 server access logs, see [Using Amazon S3 access logs to identify object access requests](using-s3-access-logs-to-identify-requests.md#using-s3-access-logs-to-identify-objects-access)\.