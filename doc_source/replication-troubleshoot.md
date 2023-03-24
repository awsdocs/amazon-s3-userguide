# Troubleshooting replication<a name="replication-troubleshoot"></a>

This section lists troubleshooting tips for Amazon S3 Replication and information about S3 Batch Replication errors\.

**Topics**
+ [Troubleshooting tips for S3 Replication](#troubleshoot-replication-tips)
+ [Batch Replication errors](#troubleshoot-batch-replication-errors)

## Troubleshooting tips for S3 Replication<a name="troubleshoot-replication-tips"></a>

If object replicas don't appear in the destination bucket after you configure replication, use these troubleshooting tips to identify and fix issues\.
+ The majority of objects replicate within 15 minutes\. The time that it takes Amazon S3 to replicate an object depends on several factors, including the source and destination Region pair, and the size of the object\. For large objects, replication can take up to several hours\. For visibility into replication times, you can [use S3 Replication Time Control \(S3 RTC\)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/replication-time-control.html#enabling-replication-time-control)\.

  If the object that is being replicated is large, wait a while before checking to see whether it appears in the destination\. You can also check the replication status of the source object\. If the object replication status is `PENDING`, Amazon S3 has not completed the replication\. If the object replication status is `FAILED`, check the replication configuration that's set on the source bucket\. Additionally, to receive information about failures during replication, you can set up Amazon S3 Event Notifications replication to receive failure events\. For more information, see [Receiving replication failure events with Amazon S3 Event Notifications](https://docs.aws.amazon.com/AmazonS3/latest/userguide/replication-metrics.html)\.
+  You can call the `HeadObject` API operation to check the replication status of an object\. The `HeadObject` API operation returns the `PENDING`,`COMPLETED`, or `FAILED` replication status of an object\. In a response to a `HeadObject` API call, the replication status is returned in the `x-amz-replication-status` element\.
**Note**  
To run `HeadObject`, you must have read access to the object that you're requesting\. A `HEAD` request has the same options as a `GET` request, without performing a `GET` operation\. For example, to run a `HeadObject` request by using the AWS Command Line Interface \(AWS CLI\), you can run the following command\. Replace the `user input placeholders` with your own information\.   

  ```
  aws s3api head-object --bucket my-bucket --key index.html
  ```
+ After `HeadObject` returns the objects with a `FAILED` replication status, you can use S3 Batch Replication to replicate those failed objects\. Alternatively, you can re\-upload the failed objects to the source bucket, which will initiate replication for the new objects\. 
+ In the replication configuration on the source bucket, verify the following:
  + The Amazon Resource Name \(ARN\) of the destination bucket is correct\.
  + The key name prefix is correct\. For example, if you set the configuration to replicate objects with the prefix `Tax`, then only objects with key names such as `Tax/document1` or `Tax/document2` are replicated\. An object with the key name `document3` is not replicated\.
  + The status of the replication rule is `Enabled`\.
+ Verify that versioning has not been suspended on any bucket in the replication configuration\. Both the source and destination buckets must have versioning enabled\.
+ If a replication rule is set to **Change object ownership to the destination bucket owner**, then the AWS Identity and Access Management \(IAM\) role that's used for replication must have the `s3:ObjectOwnerOverrideToBucketOwner` permission\. This permission is granted on the resource \(in this case, the destination bucket\)\. For example, the following `Resource` statement shows how to grant this permission on the destination bucket:

  ```
  {
    "Effect":"Allow",
    "Action":[
      "s3:ObjectOwnerOverrideToBucketOwner"
    ],
    "Resource":"arn:aws:s3:::DestinationBucket/*"
  }
  ```
+ If the destination bucket is owned by another account, the owner of the destination bucket must also grant the `s3:ObjectOwnerOverrideToBucketOwner` permission to the source bucket owner through the destination bucket policy\. To use the following example bucket policy, replace the `user input placeholders` with your own information: 

  ```
  {
    "Version": "2012-10-17",
    "Id": "Policy1644945280205",
    "Statement": [
      {
        "Sid": "Stmt1644945277847",
        "Effect": "Allow",
        "Principal": {
          "AWS": "arn:aws:iam::123456789101:role/s3-replication-role"
        },
        "Action": [
          "s3:ReplicateObject",
          "s3:ReplicateTags",
          "s3:ObjectOwnerOverrideToBucketOwner"
        ],
        "Resource": "arn:aws:s3:::DestinationBucket/*"
      }
    ]
  }
  ```
**Note**  
If the destination bucket's object ownership settings include **Bucket owner enforced**, then you don't need to update the setting to **Change object ownership to the destination bucket owner** in the replication rule\. The object ownership change will occur by default\. For more information about changing replica ownership, see [Changing the replica owner](https://docs.aws.amazon.com/AmazonS3/latest/userguide/replication-change-owner.html)\.
+ If a bucket's source objects are encrypted with an AWS Key Management Service \(AWS KMS\) key, then the replication rule must be configured to include AWS KMS\-encrypted objects\. Make sure to select **Replicate objects encrypted with AWS KMS** under your **Encryption** settings in the Amazon S3 console\. Then, select an AWS KMS key for encrypting destination objects\.
**Note**  
If the destination bucket is in a different account, specify an AWS KMS customer managed key that is owned by the destination account\. Don't use the default Amazon S3 managed key \(`aws/s3`\)\. Using the default key encrypts the objects with the Amazon S3 managed key that is owned by the source account, preventing the object from being shared with another account\. As a result, the destination account won't be able to access the objects in the destination bucket\.

  To use an AWS KMS key that belongs to the destination account to encrypt the destination objects, the destination account must grant the `kms:GenerateDataKey` and `kms:Encrypt` permissions to the replication role in the KMS key policy\. To use the following example statement in your KMS key policy, replace the `user input placeholders` with your own information:

  ```
  {    
      "Sid": "AllowS3ReplicationSourceRoleToUseTheKey",
      "Effect": "Allow",
      "Principal": {
          "AWS": "arn:aws:iam::123456789101:role/s3-replication-role"
      },
      "Action": ["kms:GenerateDataKey", "kms:Encrypt"],
      "Resource": "*"
  }
  ```

  If you use an asterisk \(`*`\) for the `Resource` statement in the AWS KMS key policy, the policy grants permission to use the KMS key to only the replication role\. The policy doesn't allow the replication role to elevate its permissions\. 

  By default, the KMS key policy grants the root user full permissions to the key\. These permissions can be delegated to other users in the same account\. Unless there are `Deny` statements in the source KMS key policy, using an IAM policy to grant the replication role permissions to the source KMS key is sufficient\.
**Note**  
KMS key policies that restrict access to specific CIDR ranges, VPC endpoints, or S3 access points can cause replication to fail\.

  If either the source or destination KMS keys grant permissions based on the encryption context, confirm that Amazon S3 Bucket Keys are turned on for the buckets\. If the buckets have S3 Bucket Keys turned on, the encryption context must be the bucket\-level resource, like this:

  ```
  "kms:EncryptionContext:arn:aws:arn": [
       "arn:aws:s3:::SOURCE_BUCKET_NAME"
       ]
  "kms:EncryptionContext:arn:aws:arn": [
       "arn:aws:s3:::DESTINATION_BUCKET_NAME"
       ]
  ```

  In addition to the permissions granted by the KMS key policy, the source account must add the following minimum permissions to the replication role's IAM policy:

  ```
  {
      "Effect": "Allow",
      "Action": [
          "kms:Decrypt",
          "kms:GenerateDataKey"
      ],
      "Resource": [
          "SourceKmsKeyArn"
      ]
  },
  {
      "Effect": "Allow",
      "Action": [
          "kms:GenerateDataKey",
          "kms:Encrypt"
      ],
      "Resource": [
          "DestinationKmsKeyArn"
      ]
  }
  ```

  For more information about how to replicate objects that are encrypted with AWS KMS, see [Replicating encrypted objects](https://docs.aws.amazon.com/AmazonS3/latest/userguide/replication-walkthrough-4.html)\.
+ If the destination bucket is owned by another AWS account, verify that the bucket owner has a bucket policy on the destination bucket that allows the source bucket owner to replicate objects\. For an example, see [Configuring replication when source and destination buckets are owned by different accounts](replication-walkthrough-2.md)\.
+ If your objects still aren't replicating after you've validated the permissions, check for any explicit `Deny` statements in the following locations:
  + `Deny` statements in the source or destination bucket policies\. Replication fails if the bucket policy denies access to the replication role for any of the following actions:

    Source bucket:

    ```
    1.            "s3:GetReplicationConfiguration",
    2.            "s3:ListBucket",
    3.            "s3:GetObjectVersionForReplication",
    4.            "s3:GetObjectVersionAcl",
    5.            "s3:GetObjectVersionTagging"
    ```

    Destination buckets:

    ```
    1.            "s3:ReplicateObject",
    2.            "s3:ReplicateDelete",
    3.            "s3:ReplicateTags"
    ```
  + `Deny` statements or permissions boundaries attached to the IAM role can cause replication to fail\.
  + `Deny` statements in AWS Organizations service control policies attached to either the source or destination accounts can cause replication to fail\.
+ If an object replica doesn't appear in the destination bucket, the following issues might have prevented replication:
  + Amazon S3 doesn't replicate an object in a source bucket that is a replica created by another replication configuration\. For example, if you set a replication configuration from bucket A to bucket B to bucket C, Amazon S3 doesn't replicate object replicas in bucket B to bucket C\.
  + A source bucket owner can grant other AWS accounts permission to upload objects\. By default, the source bucket owner doesn't have permissions for the objects created by other accounts\. The replication configuration replicates only the objects for which the source bucket owner has access permissions\. The source bucket owner can grant other AWS accounts permissions to create objects conditionally, requiring explicit access permissions on those objects\. For an example policy, see [Grant cross\-account permissions to upload objects while ensuring that the bucket owner has full control](example-bucket-policies.md#example-bucket-policies-acl-2)\.
+ Suppose that in the replication configuration, you add a rule to replicate a subset of objects that have a specific tag\. In this case, you must assign the specific tag key and value at the time the object is created in order for Amazon S3 to replicate the object\. If you first create an object and then add the tag to the existing object, Amazon S3 doesn't replicate the object\.
+ Use Amazon S3 Event Notifications to notify you of instances when objects do not replicate to their destination AWS Region\. Amazon S3 event notifications are available through Amazon Simple Queue Service \(Amazon SQS\), Amazon Simple Notification Service \(Amazon SNS\), or AWS Lambda\. For more information, see [Receiving replication failure events with Amazon S3 Event Notifications](replication-metrics.md#replication-metrics-events)\.

   You can also view replication failure reasons by using Amazon S3 Event Notifications\. To review the list of failure reasons, see [Amazon S3 replication failure reasons](https://docs.aws.amazon.com/AmazonS3/latest/userguide/replication-failure-codes.html)\.

## Batch Replication errors<a name="troubleshoot-batch-replication-errors"></a>

To troubleshoot objects that aren't replicating to the destination bucket, check the different types of permissions for the your bucket, replication role, and IAM role that's used to create the Batch Replication job\. Also, make sure to check the public access settings and bucket ownership settings\.

While using Batch Replication, you might encounter one of these errors:
+ Batch operation status is failed with reason: The job report could not be written to your report bucket\.

  This error occurs if the IAM role that's used for the Batch Operations job is unable to put the completion report into the location that was specified when you created the job\. To resolve this error, check that the IAM role has `PutObject` permissions for the bucket where you want to save the Batch Operations completion report\. It's a best practice to deliver the report to a bucket different from the source bucket\.
+ Batch operation is completed with failures and Total failed is not 0\.

  This error occurs if there are insufficient object permissions issues with the Batch Replication job that is running\. If you're using a replication rule for your Batch Replication job, make sure that the IAM role used for replication has the proper permissions to access objects from either the source or destination bucket\. You can also check the [Batch Replication completion report](https://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-batch-replication-batch.html#batch-replication-completion-report) to review the specific [Amazon S3 replication failure reason](https://docs.aws.amazon.com/AmazonS3/latest/userguide/replication-failure-codes.html)\.
+ Batch job ran successfully but the number of objects expected in destination bucket is not the same\.

  This error occurs when there's a mismatch between the objects listed in the manifest that's supplied in the Batch Replication job and the filters that you selected when you created the job\. You might also receive this message when the objects in your source bucket don't match any replication rules and aren't included in the generated manifest\.