# Enabling Amazon S3 server access logging<a name="enable-server-access-logging"></a>

Server access logging provides detailed records for the requests that are made to an Amazon S3 bucket\. Server access logs are useful for many applications\. For example, access log information can be useful in security and access audits\. It can also help you learn about your customer base and understand your Amazon S3 bill\.

By default, Amazon S3 doesn't collect server access logs\. When you enable logging, Amazon S3 delivers access logs for a source bucket to a target bucket that you choose\. The target bucket must be in the same AWS Region and AWS account as the source bucket, and must not have a default retention period configuration\. 

An access log record contains details about the requests that are made to a bucket\. This information can include the request type, the resources that are specified in the request, and the time and date that the request was processed\. For more information about logging basics, see [Logging requests using server access logging](ServerLogs.md)\. 

**Important**  
There is no extra charge for enabling server access logging on an Amazon S3 bucket\. However, any log files that the system delivers to you will accrue the usual charges for storage\. \(You can delete the log files at any time\.\) We do not assess data transfer charges for log file delivery, but we do charge the normal data transfer rate for accessing the log files\.
Your target bucket should not have server access logging enabled\. You can have logs delivered to any bucket that you own that is in the same Region as the source bucket, including the source bucket itself\. However, this would cause an infinite loop of logs and is not recommended\. For simpler log management, we recommend that you save access logs in a different bucket\. For more information, see [How do I enable log delivery?](ServerLogs.md#server-access-logging-overview)
S3 buckets with S3 Object Lock can't be used as destination buckets for server access logs\.

You can enable or disable server access logging by using the Amazon S3 console, Amazon S3 API, the AWS Command Line Interface \(AWS CLI\), or AWS SDKs\. 

Before you enable server access logging, consider the following:
+ You can use either a bucket policy or bucket access control lists \(ACL\) to grant log delivery permissions\. However, we recommend that you use a bucket policy\. If the target bucket uses the bucket owner enforced setting for Object Ownership, ACLs are disabled and no longer affect permissions\. You must use a bucket policy to grant access permissions to the logging service principal\. For more information, see [Permissions for log delivery](#grant-log-delivery-permissions-general)\.
+ Adding *deny* conditions to a bucket policy might prevent Amazon S3 from delivering access logs\.
+ You can use [default bucket encryption](https://docs.aws.amazon.com/AmazonS3/latest/userguide/default-bucket-encryption.html) on the target bucket *only* if you use **AES256 \(SSE\-S3\)**\. Default encryption with AWS KMS keys \(SSE\-KMS\) is not supported\.
+ You can't enable S3 Object Lock on the target bucket\.

## Permissions for log delivery<a name="grant-log-delivery-permissions-general"></a>

Amazon S3 uses a special log delivery account to write server access logs\. These writes are subject to the usual access control restrictions\. We recommend that you update the bucket policy on the target bucket to grant access to the logging service principal \(`logging.s3.amazonaws.com`\) for access log delivery\. 

To grant access using the bucket policy on the target bucket, you update the bucket policy to allow `s3:PutObject` access for the logging service principal\. If you use the Amazon S3 console to enable server access logging, the console automatically updates the bucket policy on the target bucket to grant these permissions to the logging service principal\. If you enable server access logging programmatically, you can manually update the bucket policy for the target bucket to grant access to the logging service principal\. 

You can alternately use bucket ACLs to grant access for access log delivery\. You add a grant entry to the bucket ACL that grants `WRITE` and `READ_ACP` permissions to the S3 log delivery group\. Granting access to the S3 log delivery group using your bucket ACL is not recommended\. For more information, see [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\.

**Bucket owner enforced setting for S3 Object Ownership**  
If the target bucket uses the bucket owner enforced setting for Object Ownership, ACLs are disabled and no longer affect permissions\. You must update the bucket policy for the target bucket to grant access to the logging service principal\. You can't update your bucket ACL to grant access to the S3 log delivery group\. You also can't include target grants in your [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketLogging.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketLogging.html) configuration\. For information about migrating existing bucket ACLs for access log delivery to a bucket policy, see [Grant access to S3 log delivery group for server access logging](object-ownership-migrating-acls-prerequisites.md#object-ownership-server-access-logs)\. For more information about Object Ownership see [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\.

### Grant permissions to the logging service principal using a bucket policy<a name="grant-log-delivery-permissions-bucket-policy"></a>

This example bucket policy grants `s3:PutObject` permissions to the logging service principal \(`logging.s3.amazonaws.com`\)\. To use this bucket policy, replace the example values\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "S3ServerAccessLogsPolicy",
            "Effect": "Allow",
            "Principal": {
                "Service": "logging.s3.amazonaws.com"
            },
            "Action": [
                "s3:PutObject"
            ],
            "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET/EXAMPLE-LOGGING-PREFIX*",
            "Condition": {
                "ArnLike": {
                    "aws:SourceArn": "arn:aws:s3:::SOURCE-BUCKET-NAME"
                },
                "StringEquals": {
                    "aws:SourceAccount": "SOURCE-ACCOUNT-ID"
                }
            }
        }
    ]
}
```

### Grant permissions to the log delivery group using the bucket ACL<a name="grant-log-delivery-permissions-acl"></a>

While we do not recommend this approach, you can grant permissions to the log delivery group using bucket ACL\. However, if the target bucket uses the bucket owner enforced setting for Object Ownership, you can't set bucket or object ACLs\. You also can't include target grants in your [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketLogging.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketLogging.html) configuration\. You must use a bucket policy to grant access to the logging service principal \(`logging.s3.amazonaws.com`\)\. For more information, see [Permissions for log delivery](#grant-log-delivery-permissions-general)\.

In the bucket ACL, the log delivery group is represented by the following URL\.

```
1. http://acs.amazonaws.com/groups/s3/LogDelivery
```

To grant `WRITE` and `READ_ACP` \(ACL read\) permissions, add the following grants to the target bucket ACL\.

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

For examples of adding ACL grants programmatically, see [Configuring ACLs](managing-acls.md)\.

**Important**  
When you enable Amazon S3 server access logging using AWS CloudFormation on a bucket and use ACLs to grant access to the S3 log delivery group, you must also add "`AccessControl": "LogDeliveryWrite"` in the property field of your bucket\. This is important because you can only grant those permissions by creating an ACL for the bucket, but you can't create custom ACLs for buckets in CloudFormation\. You can only use canned ACLs\.

## To enable server access logging<a name="enable-server-logging"></a>

Use the following examples to enable server access logging using the AWS Management Console, AWS CLI, REST API, and AWS SDK for \.NET\.

### Using the S3 console<a name="server-access-logging"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want to enable server access logging for\.

1. Choose **Properties**\.

1. In the **Server access logging** section, choose **Edit**\.

1. Under **Server access logging**, select **Enable**\. 

1. For **Target bucket**, enter the name of the bucket that you want to receive the log record objects\. 

   The target bucket must be in the same Region as the source bucket and must not have a default retention period configuration\.

1. Choose **Save changes**\.

   When you enable server access logging on a bucket, the console both enables logging on the source bucket and updates the bucket policy for the target bucket to grant `s3:PutObject` permissions to the logging service principal \(`logging.s3.amazonaws.com`\)\. For more information about this bucket policy, see [Grant permissions to the logging service principal using a bucket policy](#grant-log-delivery-permissions-bucket-policy)\.

   You can view the logs in the target bucket\.  After you enable server access logging, it might take a few hours before the logs are delivered to the target bucket\. For more information about how and when logs are delivered, see [How are logs delivered?](ServerLogs.md#how-logs-delivered)\.

For more information, see [Viewing the properties for an S3 bucket](view-bucket-properties.md)\.

### Using the REST API<a name="enable-logging-rest"></a>

To enable logging, you submit a [PUT Bucket logging](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTlogging.html) request to add the logging configuration on the source bucket\. The request specifies the target bucket and, optionally, the prefix to be used with all log object keys\. 

The following example identifies `LOGBUCKET` as the target bucket and `logs/` as the prefix\. 

```
1. <BucketLoggingStatus xmlns="http://doc.s3.amazonaws.com/2006-03-01">
2.   <LoggingEnabled>
3.     <TargetBucket>LOGBUCKET</TargetBucket>
4.     <TargetPrefix>logs/</TargetPrefix>
5.   </LoggingEnabled>
6. </BucketLoggingStatus>
```

The log objects are written and owned by the S3 log delivery account, and the bucket owner is granted full permissions on the log objects\. You can optionally use target grants to grant permissions to other users so that they can access the logs\. For more information, see [PUT Bucket logging](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTlogging.html)\. 

**Note**  
If the target bucket uses the bucket owner enforced setting for Object Ownership, you can't use target grants to grant permissions to other users\. To grant permissions to others, you can use update the bucket policy on the target bucket\. For more information, see [Permissions for log delivery](#grant-log-delivery-permissions-general)\. 

Amazon S3 also provides the [GET Bucket logging](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETlogging.html) API to retrieve logging configuration on a bucket\. To delete the logging configuration, you send the `PUT` Bucket logging request with an empty `BucketLoggingStatus`\. 

```
1. <BucketLoggingStatus xmlns="http://doc.s3.amazonaws.com/2006-03-01">
2. </BucketLoggingStatus>
```

You can use either the Amazon S3 API or the AWS SDK wrapper libraries to enable logging on a bucket\.

### Using the AWS SDKs<a name="enable-logging-sdk"></a>

------
#### [ \.NET ]

The following C\# example enables logging on a bucket\. You must create two buckets, a source bucket and a target bucket\. The example first updates the bucket ACL on the target bucket and grants the log delivery group the necessary permissions to write logs to the target bucket and then enables logging on the source bucket\. 

This example won't work on target buckets that use the bucket owner enforced setting for Object Ownership\.

If the target bucket uses the bucket owner enforced setting for Object Ownership, you can't set bucket or object ACLs\. You also can't include target grants in your [PutBucketLogging](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketLogging.html) configuration\. You must use a bucket policy to grant access to the logging service principal \(`logging.s3.amazonaws.com`\)\. For more information, see [Permissions for log delivery](#grant-log-delivery-permissions-general)\.

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

We recommend that you create a dedicated logging bucket in each AWS Region that you have S3 buckets in\. Then have the Amazon S3 access log delivered to that S3 bucket\. For more information and examples, see [https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/put-bucket-logging.html](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/put-bucket-logging.html) in the *AWS CLI Reference*\.

If the target bucket uses the bucket owner enforced setting for Object Ownership, you can't set bucket or object ACLs\. You also can't include target grants in your [PutBucketLogging](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketLogging.html) configuration\. You must use a bucket policy to grant access to the logging service principal \(`logging.s3.amazonaws.com`\)\. For more information, see [Permissions for log delivery](#grant-log-delivery-permissions-general)\.

**Example — Enable access logs with five buckets across two Regions**  
In this example, you have the following five buckets:  
+ `1-DOC-EXAMPLE-BUCKET1-us-east-1`
+ `2-DOC-EXAMPLE-BUCKET1-us-east-1`
+ `3-DOC-EXAMPLE-BUCKET1-us-east-1`
+ `1-DOC-EXAMPLE-BUCKET1-us-west-2`
+ `2-DOC-EXAMPLE-BUCKET1-us-west-2`

1. Create two logging buckets in the following Regions:
   + `DOC-EXAMPLE-BUCKET1-logs-us-east-1`
   + `DOC-EXAMPLE-BUCKET1-logs-us-west-2`

1. Then enable the Amazon S3 access logs as follows:
   + `1-DOC-EXAMPLE-BUCKET1-us-east-1` logs to the S3 bucket `DOC-EXAMPLE-BUCKET1-logs-us-east-1` with prefix `1-DOC-EXAMPLE-BUCKET1-us-east-1`
   + `2-DOC-EXAMPLE-BUCKET1-us-east-1` logs to the S3 bucket `DOC-EXAMPLE-BUCKET1-logs-us-east-1` with prefix `2-DOC-EXAMPLE-BUCKET1-us-east-1`
   + `3-DOC-EXAMPLE-BUCKET1-us-east-1` logs to the S3 bucket `DOC-EXAMPLE-BUCKET1-logs-us-east-1` with prefix `3-DOC-EXAMPLE-BUCKET1-us-east-1`
   + `1-DOC-EXAMPLE-BUCKET1-us-west-2` logs to the S3 bucket `DOC-EXAMPLE-BUCKET1-logs-us-west-2` with prefix `1-DOC-EXAMPLE-BUCKET1-us-west-2`
   + `2-DOC-EXAMPLE-BUCKET1-us-west-2` logs to the S3 bucket `DOC-EXAMPLE-BUCKET1-logs-us-west-2` with prefix `2-DOC-EXAMPLE-BUCKET1-us-west-2`

1. Grant permissions for server access log delivery using a bucket ACL or a bucket policy:
   + **Update the bucket policy** \(Recommended\) – To grant permissions to the logging service principal, use `put-bucket-policy`:

     ```
     1. aws s3api put-bucket-policy --bucket DOC-EXAMPLE-BUCKET1-logs --policy file://policy.json
     ```

     `Policy.json` is a JSON document in the current folder that contains the bucket policy\. To use this bucket policy, replace the example values\.

     ```
     {
         "Version": "2012-10-17",
         "Statement": [
             {
                 "Sid": "S3ServerAccessLogsPolicy",
                 "Effect": "Allow",
                 "Principal": {
                     "Service": "logging.s3.amazonaws.com"
                 },
                 "Action": [
                     "s3:PutObject"
                 ],
                 "Resource": "arn:aws:s3:::DOC-EXAMPLE-BUCKET1-logs/*",
                 "Condition": {
                     "ArnLike": {
                         "aws:SourceArn": "arn:aws:s3:::SOURCE-BUCKET-NAME"
                     },
                     "StringEquals": {
                         "aws:SourceAccount": "SOURCE-ACCOUNT-ID"
                     }
                 }
             }
         ]
     }
     ```
   + **Update the bucket ACL** – To grant permissions to the S3 log delivery group, use `put-bucket-acl`\.

     ```
     1. aws s3api put-bucket-acl --bucket DOC-EXAMPLE-BUCKET1-logs  --grant-write URI=http://acs.amazonaws.com/groups/s3/LogDelivery --grant-read-acp URI=http://acs.amazonaws.com/groups/s3/LogDelivery 
     ```

1. Then, apply the logging policy\.

   ```
   1. aws s3api put-bucket-logging --bucket DOC-EXAMPLE-BUCKET1 --bucket-logging-status file://logging.json 
   ```

   `Logging.json` is a JSON document in the current folder that contains the logging configuration\. If a bucket uses the bucket owner enforced setting for Object Ownership, your logging configuration can't contain target grants\. For more information, see [Permissions for log delivery](#grant-log-delivery-permissions-general)\.  
**Example – `Logging.json` without target grants**  

   The following example `Logging.json` file doesn't contain target grants and can be applied to a bucket that uses the bucket owner enforced setting for Object Ownership\.

   ```
     {
         "LoggingEnabled": {
             "TargetBucket": "DOC-EXAMPLE-BUCKET1-logs",
             "TargetPrefix": "DOC-EXAMPLE-BUCKET1/"
          }
      }
   ```  
**Example – `Logging.json` with target grants**  

   The following example `Logging.json` file contains target grants\.

   If the target bucket uses the bucket owner enforced setting for Object Ownership, you can't include target grants in your [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketLogging.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketLogging.html) configuration\. For more information, see [Permissions for log delivery](#grant-log-delivery-permissions-general)\.

   ```
     {
         "LoggingEnabled": {
             "TargetBucket": "DOC-EXAMPLE-BUCKET1-logs",
             "TargetPrefix": "DOC-EXAMPLE-BUCKET1/",
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

1. Use a bash script to add access logging for all the buckets in your account\.
**Note**  
This script only works if all your buckets are in the same Region\. If you have buckets in multiple Regions, you must adjust the script\.   
**Example – Grant access with bucket policies and add logging for the buckets in your account**  

   ```
     loggingBucket='DOC-EXAMPLE-BUCKET1-logs'
     region='us-west-2'
     
     
     # Create Logging bucket
     aws s3 mb s3://$loggingBucket --region $region
     
     aws s3api put-bucket-policy --bucket $loggingBucket --policy file://policy.json
     
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
**Example – Grant access with bucket ACLs and add logging for the buckets in your account**  

   ```
     loggingBucket='DOC-EXAMPLE-BUCKET1-logs'
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