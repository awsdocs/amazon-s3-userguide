# Enabling Amazon S3 server access logging<a name="enable-server-access-logging"></a>

Server access logging provides detailed records for the requests that are made to an Amazon S3 bucket\. Server access logs are useful for many applications\. For example, access log information can be useful in security and access audits\. It can also help you learn about your customer base and understand your Amazon S3 bill\.

By default, Amazon S3 doesn't collect server access logs\. When you enable logging, Amazon S3 delivers access logs for a source bucket to a target bucket that you choose\. The target bucket must be in the same AWS Region and AWS account as the source bucket, and must not have a default retention period configuration\. The target bucket must also not have Requester Pays enabled\. 

An access log record contains details about the requests that are made to a bucket\. This information can include the request type, the resources that are specified in the request, and the time and date that the request was processed\. For more information about logging basics, see [Logging requests using server access logging](ServerLogs.md)\. 

**Important**  
There is no extra charge for enabling server access logging on an Amazon S3 bucket\. However, any log files that the system delivers to you will accrue the usual charges for storage\. \(You can delete the log files at any time\.\) We do not assess data transfer charges for log file delivery, but we do charge the normal data transfer rate for accessing the log files\.
Your target bucket should not have server access logging enabled\. You can have logs delivered to any bucket that you own that is in the same Region as the source bucket, including the source bucket itself\. However, delivering logs to the source bucket will cause an infinite loop of logs and is not recommended\. For simpler log management, we recommend that you save access logs in a different bucket\. For more information, see [How do I enable log delivery?](ServerLogs.md#server-access-logging-overview)
S3 buckets with S3 Object Lock can't be used as destination buckets for server access logs\.

You can enable or disable server access logging by using the Amazon S3 console, Amazon S3 API, the AWS Command Line Interface \(AWS CLI\), or AWS SDKs\. 

Before you enable server access logging, consider the following:
+ You can use either a bucket policy or bucket access control lists \(ACLs\) to grant log delivery permissions\. However, we recommend that you use a bucket policy\. If the target bucket uses the bucket owner enforced setting for Object Ownership, ACLs are disabled and no longer affect permissions\. In that case, you must use a bucket policy to grant access permissions to the logging service principal\. For more information, see [Permissions for log delivery](#grant-log-delivery-permissions-general)\.
+ If there are `Deny` statements in your bucket policy, make sure that they don't prevent Amazon S3 from delivering access logs\.
+ You can use [default bucket encryption](https://docs.aws.amazon.com/AmazonS3/latest/userguide/default-bucket-encryption.html) on the target bucket *only* if you use server\-side encryption with Amazon S3 managed keys \(SSE\-S3\), which uses the 256\-bit Advanced Encryption Standard \(AES\-256\)\. Default server\-side encryption with AWS Key Management Service \(AWS KMS\) keys \(SSE\-KMS\) is not supported\.
+ You can't enable S3 Object Lock on the target bucket\.

## Permissions for log delivery<a name="grant-log-delivery-permissions-general"></a>

Amazon S3 uses a special log delivery account to write server access logs\. These writes are subject to the usual access control restrictions\. We recommend that you update the bucket policy on the target bucket to grant access to the logging service principal \(`logging.s3.amazonaws.com`\) for access log delivery\. 

To grant access by using the bucket policy on the target bucket, update the bucket policy to allow `s3:PutObject` access for the logging service principal\. If you use the Amazon S3 console to enable server access logging, the console automatically updates the bucket policy on the target bucket to grant these permissions to the logging service principal\. If you enable server access logging programmatically, you can manually update the bucket policy for the target bucket to grant access to the logging service principal\. 

You can alternately use bucket ACLs to grant access for access log delivery\. You add a grant entry to the bucket ACL that grants `WRITE` and `READ_ACP` permissions to the S3 log delivery group\. However, granting access to the S3 log delivery group by using bucket ACLs is not recommended\. For more information, see [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\.

**Bucket owner enforced setting for S3 Object Ownership**  
If the target bucket uses the bucket owner enforced setting for Object Ownership, ACLs are disabled and no longer affect permissions\. In this case, you must update the bucket policy for the target bucket to grant access to the logging service principal\. You can't update your bucket ACL to grant access to the S3 log delivery group\. You also can't include target grants in your [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketLogging.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketLogging.html) configuration\. For information about migrating existing bucket ACLs for access log delivery to a bucket policy, see [Grant access to S3 log delivery group for server access logging](object-ownership-migrating-acls-prerequisites.md#object-ownership-server-access-logs)\. For more information about Object Ownership, see [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\.

### Grant permissions to the logging service principal by using a bucket policy<a name="grant-log-delivery-permissions-bucket-policy"></a>

This example bucket policy grants `s3:PutObject` permissions to the logging service principal \(`logging.s3.amazonaws.com`\)\. To use this bucket policy, replace the `user input placeholders` with your own information\. In the following policy, `DOC-EXAMPLE-DESTINATION-BUCKET` is the target bucket where server access logs will be delivered and `DOC-EXAMPLE-SOURCE-BUCKET` is the source bucket\. 

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
            "Resource": "arn:aws:s3:::DOC-EXAMPLE-DESTINATION-BUCKET/EXAMPLE-LOGGING-PREFIX*",
            "Condition": {
                "ArnLike": {
                    "aws:SourceArn": "arn:aws:s3:::DOC-EXAMPLE-SOURCE-BUCKET"
                },
                "StringEquals": {
                    "aws:SourceAccount": "SOURCE-ACCOUNT-ID"
                }
            }
        }
    ]
}
```

### Grant permissions to the log delivery group by using a bucket ACL<a name="grant-log-delivery-permissions-acl"></a>

While we do not recommend this approach, you can grant permissions to the log delivery group by using a bucket ACL\. However, if the target bucket uses the bucket owner enforced setting for Object Ownership, you can't set bucket or object ACLs\. You also can't include target grants in your [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketLogging.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketLogging.html) configuration\. Instead, you must use a bucket policy to grant access to the logging service principal \(`logging.s3.amazonaws.com`\)\. For more information, see [Permissions for log delivery](#grant-log-delivery-permissions-general)\.

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
When you enable Amazon S3 server access logging by using AWS CloudFormation on a bucket and you're using ACLs to grant access to the S3 log delivery group, you must also add "`AccessControl": "LogDeliveryWrite"` to your CloudFormation template\. Doing so is important because you can only grant those permissions by creating an ACL for the bucket, but you can't create custom ACLs for buckets in CloudFormation\. You can only use canned ACLs with CloudFormation\.

## To enable server access logging<a name="enable-server-logging"></a>

To enable server access logging by using the AWS Management Console, AWS CLI, REST API, and AWS SDK for \.NET, use the following procedures\.

### Using the S3 console<a name="server-access-logging"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want to enable server access logging for\.

1. Choose **Properties**\.

1. In the **Server access logging** section, choose **Edit**\.

1. Under **Server access logging**, select **Enable**\. 

1. For **Target bucket**, enter the name of the bucket that you want to receive the log record objects\. 

   The target bucket must be in the same Region as the source bucket, must be owned by the same AWS account as the source bucket, and must not have Object Lock enabled or a default retention period configuration\. The target bucket must also not have Requester Pays enabled\.

1. Choose **Save changes**\.

   When you enable server access logging on a bucket, the console both enables logging on the source bucket and updates the bucket policy for the target bucket to grant `s3:PutObject` permissions to the logging service principal \(`logging.s3.amazonaws.com`\)\. For more information about this bucket policy, see [Grant permissions to the logging service principal by using a bucket policy](#grant-log-delivery-permissions-bucket-policy)\.

   You can view the logs in the target bucket\.  After you enable server access logging, it might take a few hours before the logs are delivered to the target bucket\. For more information about how and when logs are delivered, see [How are logs delivered?](ServerLogs.md#how-logs-delivered)

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
If the target bucket uses the bucket owner enforced setting for Object Ownership, you can't use target grants to grant permissions to other users\. To grant permissions to others, you can update the bucket policy on the target bucket\. For more information, see [Permissions for log delivery](#grant-log-delivery-permissions-general)\. 

Amazon S3 also provides the [GET Bucket logging](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETlogging.html) API operation to retrieve the logging configuration on a bucket\. To delete the logging configuration, you send the `PUT` Bucket logging request with an empty `BucketLoggingStatus`\. 

```
1. <BucketLoggingStatus xmlns="http://doc.s3.amazonaws.com/2006-03-01">
2. </BucketLoggingStatus>
```

To enable logging on a bucket, you can use either the Amazon S3 API or the AWS SDK wrapper libraries\.

### Using the AWS SDKs<a name="enable-logging-sdk"></a>

The following example enables logging on a bucket\. You must create two buckets, a source bucket and a target bucket\. The example first updates the bucket ACL on the target bucket and grants the log delivery group the necessary permissions to write logs to the target bucket and then enables logging on the source bucket\. 

This example won't work on target buckets that use the bucket owner enforced setting for Object Ownership\.

If the target bucket uses the bucket owner enforced setting for Object Ownership, you can't set bucket or object ACLs\. You also can't include target grants in your [PutBucketLogging](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketLogging.html) configuration\. You must use a bucket policy to grant access to the logging service principal \(`logging.s3.amazonaws.com`\)\. For more information, see [Permissions for log delivery](#grant-log-delivery-permissions-general)\.

------
#### [ \.NET ]

**AWS SDK for \.NET**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/dotnetv3/S3#code-examples)\. 
  

```
    using System;
    using System.IO;
    using System.Threading.Tasks;
    using Microsoft.Extensions.Configuration;
    using Amazon.S3;
    using Amazon.S3.Model;

    public class ServerAccessLogging
    {
        private static IConfiguration _configuration = null!;

        public static async Task Main()
        {
            LoadConfig();

            string bucketName = _configuration["BucketName"];
            string logBucketName = _configuration["LogBucketName"];
            string logObjectKeyPrefix = _configuration["LogObjectKeyPrefix"];
            string accountId = _configuration["AccountId"];

            // If the AWS Region defined for your default user is different
            // from the Region where your Amazon S3 bucket is located,
            // pass the Region name to the Amazon S3 client object's constructor.
            // For example: RegionEndpoint.USWest2 or RegionEndpoint.USEast2.
            IAmazonS3 client = new AmazonS3Client();

            try
            {
                // Update bucket policy for target bucket to allow delivery of logs to it.
                await SetBucketPolicyToAllowLogDelivery(client, bucketName, logBucketName,
                    logObjectKeyPrefix, accountId);

                // Enable logging on the source bucket.
                await EnableLoggingAsync(client, bucketName, logBucketName,
                    logObjectKeyPrefix);
            }
            catch (AmazonS3Exception e)
            {
                Console.WriteLine($"Error: {e.Message}");
            }
        }

        /// <summary>
        /// This method grants appropriate permissions for logging to the
        /// Amazon S3 bucket where the logs will be stored.
        /// </summary>
        /// <param name="client">The initialized Amazon S3 client which will be used
        /// to apply the bucket policy.</param>
        /// <param name="sourceBucketName">The name of the source bucket.</param>
        /// <param name="logBucketName">The name of the bucket where logging
        /// information will be stored.</param>
        /// <param name="logPrefix">The logging prefix where the logs should be delivered.</param>
        /// <param name="accountId">The account id of the account where the source bucket exists.</param>
        /// <returns>Async task.</returns>
        public static async Task SetBucketPolicyToAllowLogDelivery(
            IAmazonS3 client,
            string sourceBucketName,
            string logBucketName,
            string logPrefix,
            string accountId)
        {
            var resourceArn = @"""arn:aws:s3:::" + logBucketName + "/" + logPrefix + @"*""";

            var newPolicy = @"{
                                ""Statement"":[{
                                ""Sid"": ""S3ServerAccessLogsPolicy"",
                                ""Effect"": ""Allow"",
                                ""Principal"": { ""Service"": ""logging.s3.amazonaws.com"" },
                                ""Action"": [""s3:PutObject""],
                                ""Resource"": [" + resourceArn + @"],
                                ""Condition"": {
                                ""ArnLike"": { ""aws:SourceArn"": ""arn:aws:s3:::" + sourceBucketName + @""" },
                                ""StringEquals"": { ""aws:SourceAccount"": """ + accountId + @""" }
                                        }
                                    }]
                                }";
            Console.WriteLine($"The policy to apply to bucket {logBucketName} to enable logging:");
            Console.WriteLine(newPolicy);

            PutBucketPolicyRequest putRequest = new PutBucketPolicyRequest
            {
                BucketName = logBucketName,
                Policy = newPolicy,
            };
            await client.PutBucketPolicyAsync(putRequest);
            Console.WriteLine("Policy applied.");
        }

        /// <summary>
        /// This method enables logging for an Amazon S3 bucket. Logs will be stored
        /// in the bucket you selected for logging. Selected prefix
        /// will be prepended to each log object.
        /// </summary>
        /// <param name="client">The initialized Amazon S3 client which will be used
        /// to configure and apply logging to the selected Amazon S3 bucket.</param>
        /// <param name="bucketName">The name of the Amazon S3 bucket for which you
        /// wish to enable logging.</param>
        /// <param name="logBucketName">The name of the Amazon S3 bucket where logging
        /// information will be stored.</param>
        /// <param name="logObjectKeyPrefix">The prefix to prepend to each
        /// object key.</param>
        /// <returns>Async task.</returns>
        public static async Task EnableLoggingAsync(
            IAmazonS3 client,
            string bucketName,
            string logBucketName,
            string logObjectKeyPrefix)
        {
            Console.WriteLine($"Enabling logging for bucket {bucketName}.");
            var loggingConfig = new S3BucketLoggingConfig
            {
                TargetBucketName = logBucketName,
                TargetPrefix = logObjectKeyPrefix,
            };

            var putBucketLoggingRequest = new PutBucketLoggingRequest
            {
                BucketName = bucketName,
                LoggingConfig = loggingConfig,
            };
            await client.PutBucketLoggingAsync(putBucketLoggingRequest);
            Console.WriteLine($"Logging enabled.");
        }

        /// <summary>
        /// Loads configuration from settings files.
        /// </summary>
        public static void LoadConfig()
        {
            _configuration = new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("settings.json") // Load settings from .json file.
                .AddJsonFile("settings.local.json", true) // Optionally, load local settings.
                .Build();
        }
    }
```
+  For API details, see [PutBucketLogging](https://docs.aws.amazon.com/goto/DotNetSDKV3/s3-2006-03-01/PutBucketLogging) in *AWS SDK for \.NET API Reference*\. 

------

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

1. Grant permissions for server access log delivery by using a bucket ACL or a bucket policy:
   + **Update the bucket policy** \(Recommended\) – To grant permissions to the logging service principal, use the following `put-bucket-policy` command\. Replace `DOC-EXAMPLE-BUCKET1` with the name of your target bucket\.

     ```
     1. aws s3api put-bucket-policy --bucket DOC-EXAMPLE-BUCKET1-logs --policy file://policy.json
     ```

     `Policy.json` is a JSON document in the current folder that contains the bucket policy\. To use this bucket policy, replace the example values\. `user input placeholders` with your own information\. In the following policy, `DOC-EXAMPLE-DESTINATION-BUCKET` is the target bucket where server access logs will be delivered and `DOC-EXAMPLE-SOURCE-BUCKET` is the source bucket\.

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
                 "Resource": "arn:aws:s3:::DOC-EXAMPLE-DESTINATION-BUCKET-logs/*",
                 "Condition": {
                     "ArnLike": {
                         "aws:SourceArn": "arn:aws:s3:::DOC-EXAMPLE-SOURCE-BUCKET"
                     },
                     "StringEquals": {
                         "aws:SourceAccount": "SOURCE-ACCOUNT-ID"
                     }
                 }
             }
         ]
     }
     ```
   + **Update the bucket ACL** – To grant permissions to the S3 log delivery group, use the following `put-bucket-acl` command\. Replace `DOC-EXAMPLE-DESTINATION-BUCKET` with the name of your target bucket\.

     ```
     1. aws s3api put-bucket-acl --bucket DOC-EXAMPLE-DESTINATION-BUCKET-logs  --grant-write URI=http://acs.amazonaws.com/groups/s3/LogDelivery --grant-read-acp URI=http://acs.amazonaws.com/groups/s3/LogDelivery 
     ```

1. Then, apply the logging policy by using the following `put-bucket-logging` command\. Replace `DOC-EXAMPLE-DESTINATION-BUCKET` with the name of your target bucket\.

   ```
   1. aws s3api put-bucket-logging --bucket DOC-EXAMPLE-DESTINATION-BUCKET --bucket-logging-status file://logging.json 
   ```

   `Logging.json` is a JSON document in the current folder that contains the logging configuration\. If a bucket uses the bucket owner enforced setting for Object Ownership, your logging configuration can't contain target grants\. For more information, see [Permissions for log delivery](#grant-log-delivery-permissions-general)\.  
**Example – `Logging.json` without target grants**  

   The following example `Logging.json` file doesn't contain target grants and can be applied to a bucket that uses the bucket owner enforced setting for Object Ownership\.

   ```
     {
         "LoggingEnabled": {
             "TargetBucket": "DOC-EXAMPLE-DESTINATION-BUCKET-logs",
             "TargetPrefix": "DOC-EXAMPLE-DESTINATION-BUCKET/"
          }
      }
   ```  
**Example – `Logging.json` with target grants**  

   The following example `Logging.json` file contains target grants\.

   If the target bucket uses the bucket owner enforced setting for Object Ownership, you can't include target grants in your [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketLogging.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketLogging.html) configuration\. For more information, see [Permissions for log delivery](#grant-log-delivery-permissions-general)\.

   ```
     {
         "LoggingEnabled": {
             "TargetBucket": "DOC-EXAMPLE-DESTINATION-BUCKET-logs",
             "TargetPrefix": "DOC-EXAMPLE-DESTINATION-BUCKET/",
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
This script works only if all of your buckets are in the same Region\. If you have buckets in multiple Regions, you must adjust the script\.   
**Example – Grant access with bucket policies and add logging for the buckets in your account**  

   ```
     loggingBucket='DOC-EXAMPLE-DESTINATION-BUCKET-logs'
     region='us-west-2'
     
     
     # Create Logging bucket
     aws s3 mb s3://$loggingBucket --region $region
     
     aws s3api put-bucket-policy --bucket $loggingBucket --policy file://policy.json
     
     # List buckets in this account
     buckets="$(aws s3 ls | awk '{print $3}')"
     
     # Put bucket logging on each bucket
     for bucket in $buckets
         do 
           #this if statement excludes the logging bucket
           if [ "$bucket" != "$loggingBucket" ] ; then
               continue;
           fi
           printf '{
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
     loggingBucket='DOC-EXAMPLE-DESTINATION-BUCKET-logs'
     region='us-west-2'
     
     
     # Create Logging bucket
     aws s3 mb s3://$loggingBucket --region $region

     aws s3api put-bucket-acl --bucket $loggingBucket --grant-write URI=http://acs.amazonaws.com/groups/s3/LogDelivery --grant-read-acp URI=http://acs.amazonaws.com/groups/s3/LogDelivery

     # List buckets in this account
     buckets="$(aws s3 ls | awk '{print $3}')"

     # Put bucket logging on each bucket
     for bucket in $buckets
       do
       #this if statement excludes the logging bucket
         if [ "$bucket" != "$loggingBucket" ]
         then
           printf '{
             "LoggingEnabled": {
               "TargetBucket": "%s",
               "TargetPrefix": "%s/"
             }
       }' "$loggingBucket" "$bucket"  > logging.json
         aws s3api put-bucket-logging --bucket $bucket --bucket-logging-status file://logging.json
         echo "$bucket done"
       fi
     done

     rm logging.json

     echo "Complete"
   ```

## Verifying your server access logs setup<a name="verify-access-logs"></a>

After you enable server access logging, complete the following steps: 
+ Access the target bucket and verify that the log files are being delivered\. After the access logs are set up, it might take longer than an hour for all requests to be properly logged and delivered\. You can also automatically verify log delivery by using Amazon S3 request metrics and setting up Amazon CloudWatch alarms for these metrics\. For more information, see [Monitoring metrics with Amazon CloudWatch](cloudwatch-monitoring.md)\.
+ Verify that you are able to open and read the contents of the log files\.

For server access logging troubleshooting information, see [Troubleshoot server access logging](troubleshooting-server-access-logging.md)\.
