# Troubleshoot Batch Operations<a name="troubleshooting-batch-operations"></a>

The following topics list common errors to help you troubleshoot issues that you might encounter during Batch Operations\.

**Topics**
+ [Job report isn’t delivered when a retention mode is enabled](#job-report-error)
+ [Batch Replication failure: Manifest generation found no keys matching the filter criteria](#manifest-generation-no-keys)
+ [Batch Replication failures after adding a new replication rule](#new-replication-rule)
+ [S3 Batch Operations failing objects with the error 400 InvalidRequest](#invalid-request)
+ [Create job failure with job tagging enabled](#create-job)
+ [Access Denied to read the manifest](#access-denied)

## Job report isn’t delivered when an S3 Object Lock retention mode is enabled<a name="job-report-error"></a>

The following error occurs when an Object Lock retention mode \(either governance mode or compliance mode\) is enabled on the destination bucket\. When a retention mode is enabled, the bucket is write\-once\-read\-many \(WORM\) protected\. 

Error: Reasons for failure\. The job report could not be written to your report bucket\. Please check your permissions\.

The job itself is completed successfully with all objects processed\. Only the delivery of the completion report fails\. Using Object Lock with a retention mode enabled on the destination bucket is not supported\.

To fix this problem, choose a destination bucket for your job completion reports that doesn't have an Object Lock retention mode enabled\. 

## S3 Batch Replication failure with error: Manifest generation found no keys matching the filter criteria<a name="manifest-generation-no-keys"></a>

The following error occurs when objects in the source bucket are stored in the S3 Glacier Flexible Retrieval or S3 Glacier Deep Archive storage classes\.

Error: Manifest generation found no keys matching the filter criteria\.

To use Batch Replication on these objects, first restore them to the S3 Standard storage class by using an S3 Initiate Restore Object operation in a Batch Operations job\. For more information, see [Restoring an archived object](restoring-objects.md) and [Restore objects \(Batch Operations\)](batch-ops-initiate-restore-object.md)\. After you've restored the objects, you can replicate them by using a Batch Replication job\.

## Batch Operations failures occur after adding a new replication rule to an existing replication configuration<a name="new-replication-rule"></a>

Batch Operations attempts to perform existing object replication for every rule in the source bucket's replication configuration\. If there are problems with any of the existing replication rules, failures might occur\. 

The Batch Operations job's completion report explains the job failure reasons\. For a list of common errors, see [Amazon S3 replication failure reasons](replication-failure-codes.md)\.

## Batch Operations failing objects with the error 400 InvalidRequest: Task failed due to missing VersionId<a name="invalid-request"></a>

The following example error occurs if a Batch Operations job is performing actions on objects in a versioned bucket and encounters an object in the manifest with an empty version ID field\. 

Error: *BUCKET\_NAME,prefix/file\_name*,failed,400,InvalidRequest,Task failed due to missing VersionId

This error occurs because the version ID field in the manifest is an empty string, instead of the literal `null` string\.

Batch Operations will fail for that particular object or objects, but not the entire job\. This problem occurs if the manifest format is configured to use version IDs during the operation\. Non\-versioned jobs don't encounter this issue because they operate only on the most recent version of each object and ignore the version IDs in the manifest\. 

To fix this problem, convert the empty version IDs to `null` strings\. For more information, see [Converting empty version ID strings in Amazon S3 Inventory reports to null strings](inventory-configure-bops.md)\.

## Create job failure with job tag option enabled<a name="create-job"></a>

Without the `s3:PutJobTagging` permission, creating Batch Operations jobs with the job tag option enabled causes `403 access denied` errors\.

To create Batch Operations jobs with the job tag option enabled, the AWS Identity and Access Management \(IAM\) user that's creating the Batch Operations job must have the `s3:PutJobTagging` permission in addition to the `s3:CreateJob` permission\.

For more information about the permissions required for Batch Operations, see [Granting permissions for Amazon S3 Batch Operations](batch-ops-iam-role-policies.md)\. 



## Access Denied to read the manifest<a name="access-denied"></a>

If Batch Operations can't read the manifest file when you attempt to create a Batch Operations job, the following errors can occur\.

**AWS CLI**  
Reason for failure Reading the manifest is forbidden: AccessDenied

**Amazon S3 console**  
Warning: Unable to get the manifest object's ETag\. Specify a different object to continue\.

To solve this problem, do the following: 
+ Verify that the IAM role for the AWS account that you used to create the Batch Operations job has `s3:GetObject` permissions\. The account's IAM role must have `s3:GetObject` permissions to allow Batch Operations to read the manifest file\.

  For more information about the permissions required for Batch Operations, see [Granting permissions for Amazon S3 Batch Operations](batch-ops-iam-role-policies.md)\. 
+  Check the manifest objects' metadata for any access mismatches with S3 Object Ownership\. For more information about S3 Object Ownership, see [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\.
+ Check whether AWS Key Management Service \(AWS KMS\) keys are used to encrypt the manifest file\.

  Batch Operations support *CSV inventory reports* that are AWS KMS\-encrypted\. However, Batch Operations don't support *CSV manifest files* that are AWS KMS\-encrypted\. For more information, see [Configuring Amazon S3 Inventory](configure-inventory.md) and [Specifying a manifest](batch-ops-create-job.md#specify-batchjob-manifest)\.