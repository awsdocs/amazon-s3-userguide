--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Enabling Amazon S3 server access logging<a name="enable-server-access-logging"></a>

Server access logging provides detailed records for the requests that are made to an Amazon S3 bucket\. Server access logs are useful for many applications\. For example, access log information can be useful in security and access audits\. It can also help you learn about your customer base and understand your Amazon S3 bill\.

By default, Amazon S3 doesn't collect server access logs\. When you enable logging, Amazon S3 delivers access logs for a source bucket to a target bucket that you choose\. The target bucket must be in the same AWS Region as the source bucket and must not have a default retention period configuration\. 

An access log record contains details about the requests that are made to a bucket\. This information can include the request type, the resources that are specified in the request, and the time and date that the request was processed\. For more information about logging basics, see [Logging requests using server access logging](ServerLogs.md)\. 

**Important**  
There is no extra charge for enabling server access logging on an Amazon S3 bucket\. However, any log files that the system delivers to you will accrue the usual charges for storage\. \(You can delete the log files at any time\.\) We do not assess data transfer charges for log file delivery, but we do charge the normal data transfer rate for accessing the log files\.

You can enable or disable server access logging by using the Amazon S3 console, Amazon S3 API, the AWS Command Line Interface \(AWS CLI\), or AWS SDKs\. 

Before you enable server access logging, consider the following:
+ In Amazon S3, you can grant permission to deliver access logs through bucket access control lists \(ACLs\), but not through bucket policy\.
+ Adding *deny* conditions to a bucket policy might prevent Amazon S3 from delivering access logs\.
+ You can use [default bucket encryption](https://docs.aws.amazon.com/AmazonS3/latest/userguide/default-bucket-encryption.html) on the target bucket *only* if **AES256 \(SSE\-S3\)** is selected\. SSE\-KMS encryption is not supported\. 
+ You can't enable S3 Object Lock on the target bucket\.

To enable logging, follow these steps\.

**Topics**
+ [Step 1: Enable server access logging](#enable-sever-access-logs)
+ [Step 2: Grant the log delivery group WRITE and READ\_ACP permissions](#grant-log-delivery-permissions-general)

## Step 1: Enable server access logging<a name="enable-sever-access-logs"></a>

You can enable logging using the console, API, AWS CLI, or AWS SDKs\.

### Using the S3 console<a name="server-access-logging"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want to enable server access logging for\.

1. Choose **Properties**\.

1. In the **Server access logging** section, choose **Edit**\.

1. Under **Server access logging**, select **Enable**\. For **Target bucket**, enter the name of the bucket that you want to receive the log record objects\. The target bucket must be in the same Region as the source bucket and must not have a default retention period configuration\.

1. Choose **Save changes**\.

   You can view the logs in the target bucket\.  After you enable server access logging, it might take a few hours before the logs are delivered to the target bucket\. For more information about how and when logs are delivered, see [How are logs delivered?](ServerLogs.md#how-logs-delivered)\.

When you enable logging on a bucket, the console both enables logging on the source bucket and adds a grant in the target bucket's access control list \(ACL\) granting write permission to the Log Delivery group\. 

For more information, see [Viewing the properties for an S3 bucket](view-bucket-properties.md)\.

### Using the REST API<a name="enable-logging-rest"></a>



To enable logging, you submit a [PUT Bucket logging](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTlogging.html) request to add the logging configuration on the source bucket\. The request specifies the target bucket and, optionally, the prefix to be used with all log object keys\. 

The following example identifies `logbucket` as the target bucket and `logs/` as the prefix\. 

```
1. <BucketLoggingStatus xmlns="http://doc.s3.amazonaws.com/2006-03-01">
2.   <LoggingEnabled>
3.     <TargetBucket>logbucket</TargetBucket>
4.     <TargetPrefix>logs/</TargetPrefix>
5.   </LoggingEnabled>
6. </BucketLoggingStatus>
```

The log objects are written and owned by the Log Delivery account, and the bucket owner is granted full permissions on the log objects\. In addition, you can optionally grant permissions to other users so that they can access the logs\. For more information, see [PUT Bucket logging](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTlogging.html)\. 

Amazon S3 also provides the [GET Bucket logging](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETlogging.html) API to retrieve logging configuration on a bucket\. To delete the logging configuration, you send the PUT Bucket logging request with an empty `BucketLoggingStatus`\. 

```
1. <BucketLoggingStatus xmlns="http://doc.s3.amazonaws.com/2006-03-01">
2. </BucketLoggingStatus>
```

You can use either the Amazon S3 API or the AWS SDK wrapper libraries to enable logging on a bucket\.

### Using the AWS SDKs<a name="enable-logging-sdk"></a>

------
#### [ \.NET ]

The following C\# example enables logging on a bucket\. You must create two buckets, a source bucket and a target bucket\. The example first grants the Log Delivery group the necessary permission to write logs to the target bucket and then enables logging on the source bucket\.

------

**Example**  

```
 1. using Amazon;
 2. using Amazon.S3;
 3. using Amazon.S3.Model;
 4. using System;
 5. using System.Threading.Tasks;
 6. 
 7. namespace Amazon.DocSamples.S3
 8. {
 9.     class ServerAccesLoggingTest
10.     {
11.         private const string bucketName = "*** bucket name for which to enable logging ***"; 
12.         private const string targetBucketName = "*** bucket name where you want access logs stored ***"; 
13.         private const string logObjectKeyPrefix = "Logs";
14.         // Specify your bucket region (an example region is shown).
15.         private static readonly RegionEndpoint bucketRegion = RegionEndpoint.USWest2;
16.         private static IAmazonS3 client;
17. 
18.         public static void Main()
19.         {
20.             client = new AmazonS3Client(bucketRegion);
21.             EnableLoggingAsync().Wait();
22.         }
23. 
24.         private static async Task EnableLoggingAsync()
25.         {
26.             try
27.             {
28.                 // Step 1 - Grant Log Delivery group permission to write log to the target bucket.
29.                 await GrantPermissionsToWriteLogsAsync();
30.                 // Step 2 - Enable logging on the source bucket.
31.                 await EnableDisableLoggingAsync();
32.             }
33.             catch (AmazonS3Exception e)
34.             {
35.                 Console.WriteLine("Error encountered on server. Message:'{0}' when writing an object", e.Message);
36.             }
37.             catch (Exception e)
38.             {
39.                 Console.WriteLine("Unknown encountered on server. Message:'{0}' when writing an object", e.Message);
40.             }
41.         }
42. 
43.         private static async Task GrantPermissionsToWriteLogsAsync()
44.         {
45.             var bucketACL = new S3AccessControlList();
46.             var aclResponse = client.GetACL(new GetACLRequest { BucketName = targetBucketName });
47.             bucketACL = aclResponse.AccessControlList;
48.             bucketACL.AddGrant(new S3Grantee { URI = "http://acs.amazonaws.com/groups/s3/LogDelivery" }, S3Permission.WRITE);
49.             bucketACL.AddGrant(new S3Grantee { URI = "http://acs.amazonaws.com/groups/s3/LogDelivery" }, S3Permission.READ_ACP);
50.             var setACLRequest = new PutACLRequest
51.             {
52.                 AccessControlList = bucketACL,
53.                 BucketName = targetBucketName
54.             };
55.             await client.PutACLAsync(setACLRequest);
56.         }
57. 
58.         private static async Task EnableDisableLoggingAsync()
59.         {
60.             var loggingConfig = new S3BucketLoggingConfig
61.             {
62.                 TargetBucketName = targetBucketName,
63.                 TargetPrefix = logObjectKeyPrefix
64.             };
65. 
66.             // Send request.
67.             var putBucketLoggingRequest = new PutBucketLoggingRequest
68.             {
69.                 BucketName = bucketName,
70.                 LoggingConfig = loggingConfig
71.             };
72.             await client.PutBucketLoggingAsync(putBucketLoggingRequest);
73.         }
74.     }
75. }
```

### Using the AWS CLI<a name="enabling-s3-access-logs-for-requests"></a>

We recommend that you create a dedicated logging bucket in each AWS Region that you have S3 buckets in\. Then have the Amazon S3 access log delivered to that S3 bucket\.

**Example — Enable access logs with five buckets across two Regions**  
In this example, you have the following five buckets:  
+ `1-awsexamplebucket1-us-east-1`
+ `2-awsexamplebucket1-us-east-1`
+ `3-awsexamplebucket1-us-east-1`
+ `1-awsexamplebucket1-us-west-2`
+ `2-awsexamplebucket1-us-west-2`

1. Create two logging buckets in the following Regions:
   + `awsexamplebucket1-logs-us-east-1`
   + `awsexamplebucket1-logs-us-west-2`

1. Then enable the Amazon S3 access logs as follows:
   + `3-awsexamplebucket1-us-east-1` logs to the S3 bucket `awsexamplebucket1-logs-us-east-1` with prefix `1-awsexamplebucket1-us-east-1`
   + `2-awsexamplebucket1-us-east-1` logs to the S3 bucket `awsexamplebucket1-logs-us-east-1` with prefix `2-awsexamplebucket1-us-east-1`
   + `3-awsexamplebucket1-us-east-1` logs to the S3 bucket `awsexamplebucket1-logs-us-east-1` with prefix `3-awsexamplebucket1-us-east-1`
   + `1-awsexamplebucket1-us-west-2` logs to the S3 bucket `awsexamplebucket1-logs-us-west-2` with prefix `1-awsexamplebucket1-us-west-2`
   + `2-awsexamplebucket1-us-west-2` logs to the S3 bucket `awsexamplebucket1-logs-us-west-2` with prefix `2-awsexamplebucket1-us-west-2`

1. You can then enable the Amazon S3 access logs using the following methods:
   + Using the [Enabling Amazon S3 server access logging](#enable-server-access-logging) or,
   + Using the [AWS CLI put\-bucket\-logging command](https://docs.aws.amazon.com/cli/latest/reference/s3api/put-bucket-logging.html) to programmatically enable access logs on a bucket using the following commands:

   1. First, grant Amazon S3 permission using `put-bucket-acl`\.

      ```
      1.  aws s3api put-bucket-acl --bucket awsexamplebucket1-logs  --grant-write URI=http://acs.amazonaws.com/groups/s3/LogDelivery --grant-read-acp URI=http://acs.amazonaws.com/groups/s3/LogDelivery 
      ```

   1. Then, apply the logging policy\.

      ```
      1. aws s3api put-bucket-logging --bucket awsexamplebucket1 --bucket-logging-status file://logging.json 
      ```

      `Logging.json` is a JSON document in the current folder that contains the logging policy\.

      ```
        {
            "LoggingEnabled": {
                "TargetBucket": "awsexamplebucket1-logs",
                "TargetPrefix": "awsexamplebucket1/",
                "TargetGrants": [
                     {
                        "Grantee": {
                            "Type": "AmazonCustomerByEmail",
                            "EmailAddress": "user@example.com"
                         },
                        "Permission": "FULL_CONTROL"
                     }
                 ]
             }
         }
      ```
**Note**  
The `put-bucket-acl` command is required to grant the Amazon S3 log delivery system the necessary permissions \(write and read\-acp permissions\)\. 

   1. Use a bash script to add access logging for all the buckets in your account\.

      ```
        loggingBucket='awsexamplebucket1-logs'
        region='us-west-2'
        
        
        # Create Logging bucket
        aws s3 mb s3://$loggingBucket --region $region
        
        aws s3api put-bucket-acl --bucket $loggingBucket --grant-write URI=http://acs.amazonaws.com/groups/s3/LogDelivery --grant-read-acp URI=http://acs.amazonaws.com/groups/s3/LogDelivery
        
        # List buckets in this account
        buckets="$(aws s3 ls | awk '{print $3}')"
        
        # Put bucket logging on each bucket
        for bucket in $buckets
            do printf '{
           "LoggingEnabled": {
               "TargetBucket": "%s",
               "TargetPrefix": "%s/"
                }
            }' "$loggingBucket" "$bucket"  > logging.json
            aws s3api put-bucket-logging --bucket $bucket --bucket-logging-status file://logging.json
            echo "$bucket done"
        done
        
        rm logging.json
        
        echo "Complete"
      ```
**Note**  
This only works if all your buckets are in the same Region\. If you have buckets in multiple Regions, you must adjust the script\. 

## Step 2: Grant the log delivery group WRITE and READ\_ACP permissions<a name="grant-log-delivery-permissions-general"></a>

Amazon S3 writes the log files to the target bucket as a member of the predefined Amazon S3 group Log Delivery\. These writes are subject to the usual access control restrictions\. You must grant `s3:GetObjectAcl` and `s3:PutObject` permissions to this group by adding grants to the access control list \(ACL\) of the target bucket\. The Log Delivery group is represented by the following URL\. 

```
1. http://acs.amazonaws.com/groups/s3/LogDelivery
```

To grant `WRITE` and `READ_ACP` permissions, add the following grants\. For information about ACLs, see [Access control list \(ACL\) overview](acl-overview.md)\.

```
 1. <Grant>
 2.     <Grantee xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  xsi:type="Group">
 3.         <URI>http://acs.amazonaws.com/groups/s3/LogDelivery</URI> 
 4.     </Grantee>
 5.     <Permission>WRITE</Permission>
 6. </Grant>
 7. <Grant>
 8.     <Grantee xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  xsi:type="Group">
 9.         <URI>http://acs.amazonaws.com/groups/s3/LogDelivery</URI> 
10.     </Grantee>
11.     <Permission>READ_ACP</Permission>
12. </Grant>
```

For examples of adding ACL grants programmatically using the AWS SDKs, see [Configuring ACLs](managing-acls.md)\.