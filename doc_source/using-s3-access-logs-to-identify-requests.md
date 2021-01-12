--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Using Amazon S3 access logs to identify requests<a name="using-s3-access-logs-to-identify-requests"></a>

You can identify Amazon S3 requests with Amazon S3 access logs\. 

**Note**  
We recommend that you use AWS CloudTrail data events instead of Amazon S3 access logs\. CloudTrail data events are easier to set up and contain more information\. For more information, see [Identifying Amazon S3 requests using CloudTrail](cloudtrail-request-identification.md)\.
Depending on how many access requests you get, it may require more resources and/or more time to analyze your logs\.

**Topics**
+ [Querying Amazon S3 access logs for requests using Amazon Athena](querying-s3-access-logs-for-requests.md)
+ [Using Amazon S3 access logs to identify signature version 2 requests](using-s3-access-logs-to-identify-sigv2-requests.md)
+ [Using Amazon S3 access logs to identify object access requests](#using-s3-access-logs-to-identify-objects-access)

**Note**  
To reduce the time that you retain your log, you can create an Amazon S3 lifecycle policy for your server access logs bucket\. Configure the lifecycle policy to remove log files periodically\. Doing so reduces the amount of data that Athena analyzes for each query\. For more information, see [Setting lifecycle configuration on a bucket](how-to-set-lifecycle-configuration-intro.md)\.

## Using Amazon S3 access logs to identify object access requests<a name="using-s3-access-logs-to-identify-objects-access"></a>

You can use queries on Amazon S3 server access logs to identify Amazon S3 object access requests, for operations such as GET, PUT, and DELETE, and discover further information about those requests\.

The following Amazon Athena query example shows how to get all PUT object requests for Amazon S3 from the server access log\. 

**Example — show all requesters that are sending PUT object requests in a certain period**  

```
SELECT Bucket, Requester, RemoteIP, Key, HTTPStatus, ErrorCode, RequestDateTime
FROM s3_access_logs_db
WHERE Operation='REST.PUT.OBJECT' AND
parse_datetime(RequestDateTime,'dd/MMM/yyyy:HH:mm:ss Z') 
BETWEEN parse_datetime('2019-07-01:00:42:42','yyyy-MM-dd:HH:mm:ss')
AND 
parse_datetime('2019-07-02:00:42:42','yyyy-MM-dd:HH:mm:ss')
```

The following Amazon Athena query example shows how to get all GET object requests for Amazon S3 from the server access log\. 

**Example — show all requesters that are sending GET object requests in a certain period**  

```
SELECT Bucket, Requester, RemoteIP, Key, HTTPStatus, ErrorCode, RequestDateTime
FROM s3_access_logs_db
WHERE Operation='REST.GET.OBJECT' AND
parse_datetime(RequestDateTime,'dd/MMM/yyyy:HH:mm:ss Z') 
BETWEEN parse_datetime('2019-07-01:00:42:42','yyyy-MM-dd:HH:mm:ss')
AND 
parse_datetime('2019-07-02:00:42:42','yyyy-MM-dd:HH:mm:ss')
```

The following Amazon Athena query example shows how to get all anonymous requests to your S3 buckets from the server access log\. 

**Example — show all anonymous requesters that are making requests to a bucket in a certain period**  

```
SELECT Bucket, Requester, RemoteIP, Key, HTTPStatus, ErrorCode, RequestDateTime
FROM s3_access_logs_db.mybucket_logs
WHERE Requester IS NULL AND
parse_datetime(RequestDateTime,'dd/MMM/yyyy:HH:mm:ss Z') 
BETWEEN parse_datetime('2019-07-01:00:42:42','yyyy-MM-dd:HH:mm:ss')
AND 
parse_datetime('2019-07-02:00:42:42','yyyy-MM-dd:HH:mm:ss')
```

**Note**  
You can modify the date range as needed to suit your needs\.
These query examples may also be useful for security monitoring\. You can review the results for `PutObject` or `GetObject` calls from unexpected or unauthorized IP addresses/requesters and for identifying any anonymous requests to your buckets\.
This query only retrieves information from the time at which logging was enabled\. 
If you are using Amazon S3 AWS CloudTrail logs, see [Using AWS CloudTrail to identify access to Amazon S3 objects](cloudtrail-identification-object-access.md)\. 