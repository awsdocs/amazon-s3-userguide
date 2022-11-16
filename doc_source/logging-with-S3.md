# Logging options for Amazon S3<a name="logging-with-S3"></a>

You can record the actions that are taken by users, roles, or AWS services on Amazon S3 resources and maintain log records for auditing and compliance purposes\. To do this, you can use server\-access logging, AWS CloudTrail logging, or a combination of both\. We recommend that you use CloudTrail for logging bucket\-level and object\-level actions for your Amazon S3 resources\. For more information about each option, see the following sections:
+ [Logging requests using server access logging](ServerLogs.md)
+ [Logging Amazon S3 API calls using AWS CloudTrail](cloudtrail-logging.md)

The following table lists the key properties of CloudTrail logs and Amazon S3 server\-access logs\. To make sure that CloudTrail meets your security requirements, review the table and notes\.


| Log properties | AWS CloudTrail | Amazon S3 server logs | 
| --- |--- |--- |
|  Can be forwarded to other systems \(Amazon CloudWatch Logs, Amazon CloudWatch Events\)  |  Yes  | No | 
|  Deliver logs to more than one destination \(for example, send the same logs to two different buckets\)  |  Yes  | No | 
|  Turn on logs for a subset of objects \(prefix\)  |  Yes  | No | 
|  Cross\-account log delivery \(target and source bucket owned by different accounts\)  |  Yes  | No | 
|  Integrity validation of log file by using digital signature or hashing  |  Yes  | No | 
|  Default or choice of encryption for log files  |  Yes  | No | 
|  Object operations \(by using Amazon S3 APIs\)  |  Yes  |  Yes  | 
|  Bucket operations \(by using Amazon S3 APIs\)  |  Yes  |  Yes  | 
|  Searchable UI for logs  |  Yes  | No | 
|  Fields for Object Lock parameters, Amazon S3 Select properties for log records  |  Yes  | No | 
|  Fields for `Object Size`, `Total Time`, `Turn-Around Time`, and `HTTP Referer` for log records  |  |  Yes  | 
|  Lifecycle transitions, expirations, restores  |  |  Yes  | 
|  Logging of keys in a batch delete operation  |  |  Yes  | 
|  Authentication failures1  |  |  Yes  | 
|  Accounts where logs get delivered  |  Bucket owner2, and requester  |  Bucket owner only  | 
| Performance and Cost | AWS CloudTrail | Amazon S3 Server Logs | 
| --- |--- |--- |
|  Price  |  Management events \(first delivery\) are free; data events incur a fee, in addition to storage of logs  |  No other cost in addition to storage of logs  | 
|  Speed of log delivery  |  Data events every 5 minutes; management events every 15 minutes  |  Within a few hours  | 
|  Log format  |  JSON  |  Log file with space\-separated, newline\-delimited records  | 

**Notes**

1. CloudTrail does not deliver logs for requests that fail authentication \(in which the provided credentials are not valid\)\. However, it does include logs for requests in which authorization fails \(`AccessDenied`\) and requests that are made by anonymous users\.

1. The S3 bucket owner receives CloudTrail logs when the account does not have full access to the object in the request\. For more information, see [Object\-level actions in cross\-account scenarios](cloudtrail-logging-s3-info.md#cloudtrail-object-level-crossaccount)\. 