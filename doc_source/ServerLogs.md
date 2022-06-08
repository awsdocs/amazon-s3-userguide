# Logging requests using server access logging<a name="ServerLogs"></a>

Server access logging provides detailed records for the requests that are made to a bucket\. Server access logs are useful for many applications\. For example, access log information can be useful in security and access audits\. It can also help you learn about your customer base and understand your Amazon S3 bill\.

**Note**  
Server access logs don't record information about wrong\-region redirect errors for Regions that launched after March 20, 2019\. Wrong\-region redirect errors occur when a request for an object or bucket is made outside the Region in which the bucket exists\. 

## How do I enable log delivery?<a name="server-access-logging-overview"></a>

To enable log delivery, perform the following basic steps\. For details, see [Enabling Amazon S3 server access logging](enable-server-access-logging.md)\.

1. **Provide the name of the target bucket**\. This bucket is where you want Amazon S3 to save the access logs as objects\. Both the source and target buckets must be in the same AWS Region and owned by the same account\. 

   You can have logs delivered to any bucket that you own that is in the same Region as the source bucket, including the source bucket itself\. But for simpler log management, we recommend that you save access logs in a different bucket\. 

   When your source bucket and target bucket are the same bucket, additional logs are created for the logs that are written to the bucket\. We do not recommend doing this because it could result in a small increase in your storage billing\. In addition, the extra logs about logs might make it harder to find the log that you are looking for\. If you choose to save access logs in the source bucket, we recommend that you specify a prefix for all log object keys so that the object names begin with a common string and the log objects are easier to identify\. 

   [Key prefixes](https://docs.aws.amazon.com/general/latest/gr/glos-chap.html#keyprefix) are also useful to distinguish between source buckets when multiple buckets log to the same target bucket\.

1. **\(Optional\) Assign a prefix to all Amazon S3 log object keys\.** The prefix makes it simpler for you to locate the log objects\. For example, if you specify the prefix value `logs/`, each log object that Amazon S3 creates begins with the `logs/` prefix in its key\.

   ```
   logs/2013-11-01-21-32-16-E568B2907131C0C0
   ```

   The key prefix can also help when you delete the logs\. For example, you can set a lifecycle configuration rule for Amazon S3 to delete objects with a specific key prefix\. For more information, see [Deleting Amazon S3 log files](deleting-log-files-lifecycle.md)\.

1. **\(Optional\) Set permissions in target grants so that others can access the generated logs\.** By default, only the bucket owner always has full access to the log objects\. If your target bucket \(where your server access logs are stored\) uses the bucket owner enforced setting for S3 Object Ownership to disable access control lists \(ACLs\), you can't grant permissions in target grants that use ACLs\. However, you can update your bucket policy for the target bucket to grant access to others\. For more information, see [Identity and access management in Amazon S3](s3-access-control.md) and [Permissions for log delivery](enable-server-access-logging.md#grant-log-delivery-permissions-general)\. 

## Log object key format<a name="server-log-keyname-format"></a>

Amazon S3 uses the following object key format for the log objects it uploads in the target bucket:

```
TargetPrefixYYYY-mm-DD-HH-MM-SS-UniqueString/
```

In the key, `YYYY`, `mm`, `DD`, `HH`, `MM`, and `SS` are the digits of the year, month, day, hour, minute, and seconds \(respectively\) when the log file was delivered\. These dates and times are in Coordinated Universal Time \(UTC\)\. 

A log file delivered at a specific time can contain records written at any point before that time\. There is no way to know whether all log records for a certain time interval have been delivered or not\. 

The `UniqueString` component of the key is there to prevent overwriting of files\. It has no meaning, and log processing software should ignore it\. 

The trailing slash */* is required to denote the end of the prefix\.

## How are logs delivered?<a name="how-logs-delivered"></a>

Amazon S3 periodically collects access log records, consolidates the records in log files, and then uploads log files to your target bucket as log objects\. If you enable logging on multiple source buckets that identify the same target bucket, the target bucket will have access logs for all those source buckets\. However, each log object reports access log records for a specific source bucket\. 

Amazon S3 uses a special log delivery account to write server access logs\. These writes are subject to the usual access control restrictions\. We recommend that you update the bucket policy on the target bucket to grant access to the logging service principal \(`logging.s3.amazonaws.com`\) for access log delivery\. However, you can also grant access for access log delivery to the S3 log delivery group through your bucket access control list \(ACL\)\. Granting access to the S3 log delivery group using your bucket ACL is not recommended\. 

When you enable server access logging and grant access for access log delivery through your bucket policy, you update the bucket policy on the target bucket to allow `s3:PutObject` access for the logging service principal\. If you use the Amazon S3 console to enable server access logging on a bucket, the console automatically updates the bucket policy on the target bucket to grant these permissions to the logging service principal\. For more information about granting permissions for server access log delivery, see [Permissions for log delivery](enable-server-access-logging.md#grant-log-delivery-permissions-general)\. 

**Bucket owner enforced setting for S3 Object Ownership**  
If the target bucket uses the bucket owner enforced setting for Object Ownership, ACLs are disabled and no longer affect permissions\. You must update the bucket policy on the target bucket to grant access to the logging service principal\. For more information about Object Ownership, see [Grant access to S3 log delivery group for server access logging](object-ownership-migrating-acls-prerequisites.md#object-ownership-server-access-logs)\.

## Best effort server log delivery<a name="LogDeliveryBestEffort"></a>

Server access log records are delivered on a best effort basis\. Most requests for a bucket that is properly configured for logging result in a delivered log record\. Most log records are delivered within a few hours of the time that they are recorded, but they can be delivered more frequently\. 

The completeness and timeliness of server logging is not guaranteed\. The log record for a particular request might be delivered long after the request was actually processed, or *it might not be delivered at all*\. The purpose of server logs is to give you an idea of the nature of traffic against your bucket\. It is rare to lose log records, but server logging is not meant to be a complete accounting of all requests\. 

It follows from the best\-effort nature of the server logging feature that the usage reports available at the AWS portal \(Billing and Cost Management reports on the [AWS Management Console](https://console.aws.amazon.com/)\) might include one or more access requests that do not appear in a delivered server log\. 

## Bucket logging status changes take effect over time<a name="BucketLoggingStatusChanges"></a>

Changes to the logging status of a bucket take time to actually affect the delivery of log files\. For example, if you enable logging for a bucket, some requests made in the following hour might be logged, while others might not\. If you change the target bucket for logging from bucket A to bucket B, some logs for the next hour might continue to be delivered to bucket A, while others might be delivered to the new target bucket B\. In all cases, the new settings eventually take effect without any further action on your part\. 

For more information about logging and log files, see the following sections:

**Topics**
+ [How do I enable log delivery?](#server-access-logging-overview)
+ [Log object key format](#server-log-keyname-format)
+ [How are logs delivered?](#how-logs-delivered)
+ [Best effort server log delivery](#LogDeliveryBestEffort)
+ [Bucket logging status changes take effect over time](#BucketLoggingStatusChanges)
+ [Enabling Amazon S3 server access logging](enable-server-access-logging.md)
+ [Amazon S3 server access log format](LogFormat.md)
+ [Deleting Amazon S3 log files](deleting-log-files-lifecycle.md)
+ [Using Amazon S3 access logs to identify requests](using-s3-access-logs-to-identify-requests.md)