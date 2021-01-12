--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Using Amazon S3 access logs to identify signature version 2 requests<a name="using-s3-access-logs-to-identify-sigv2-requests"></a>

Amazon S3 support for Signature Version 2 will be turned off \(deprecated\)\. After that, Amazon S3 will no longer accept requests that use Signature Version 2, and all requests must use *Signature Version 4* signing\. You can identify Signature Version 2 access requests using Amazon S3 access logs\. 

**Note**  
We recommend that you use AWS CloudTrail data events instead of Amazon S3 access logs\. CloudTrail data events are easier to set up and contain more information\. For more information, see [Using AWS CloudTrail to identify Amazon S3 signature version 2 requests ](cloudtrail-identification-sigv2-requests.md)\.

**Example — show all requesters that are sending signature version 2 traffic**  

```
                   SELECT requester, Sigv, Count(Sigv) as SigCount 
                   FROM s3_access_logs_db.mybucket_logs
                   GROUP BY requester, Sigv;
```