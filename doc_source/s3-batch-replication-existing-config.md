# Create a Batch Replication job for existing replication rules<a name="s3-batch-replication-existing-config"></a>

You can configure S3 Batch Replication for an existing replication configuration by using the AWS SDKs, AWS Command Line Interface \(AWS CLI\), or the Amazon S3 console\. For an overview of Batch Replication see, [Replicating existing objects with S3 Batch Replication](s3-batch-replication-batch.md)\.

As a prerequisite, you must create a Batch Operations AWS Identity and Access Management \(IAM\) role to grant Amazon S3 permissions to perform actions on your behalf, see [Configuring IAM policies for Batch Replication](s3-batch-replication-policies.md)\.

When the Batch Replication job finishes, you receive a completion report\. For more information about how to use the report to examine the job, see [Tracking job status and completion reports](batch-ops-job-status.md)\.

## Using the S3 console<a name="batch-replication-existing-config-console"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose **Batch Operations** on the navigation pane of the Amazon S3 console\.

1. Choose **Create job**\.

1. Choose the **Region** where you want to create your job\.

1. Select the **Manifest format**\. This example will show how to create a manifest based on an existing S3 replication configuration\. 
**Note**  
The manifest is a list of all of the objects that you want to run the specified action on\. To learn more about Batch Operations manifests, see [Specifying a manifest](batch-ops-create-job.md#specify-batchjob-manifest)\. If you have a manifest prepared, choose **S3 inventory report \(manifest\.json\)** or **CSV**\. If the objects in your manifest are in a versioned bucket, you should specify the version IDs for the objects\. For more information about creating a manifest see, [Specifying a manifest](batch-ops-create-job.md#specify-batchjob-manifest)\.

1. To create a manifest based on your replication configuration, choose **Create manifest using S3 Replication configuration**\. Then chose the source bucket of your replication configuration\.

1. \(Optional\) You may include additional filters such as object creation date and replication status\. For examples on how to filter by replication status see, [Specifying a manifest for a Batch Replication job](s3-batch-replication-batch.md#batch-replication-manifest)\. 

1. To save a manifest, select **Save Batch Operations manifest**\.

   1. If you choose to generate and save a manifest, you must choose either **Bucket in this account** or **Bucket in another AWS account**\. Specify the bucket name in the text box\.
**Note**  
The generated manifest must be stored in the same AWS Region as the source bucket\.

   1. Choose the **Encryption key type**\.

1. \(Optional\) Provide a **Description**\. 

1. Adjust the **Priority** of the job if needed\. Higher numbers indicate higher priority\. Amazon S3 attempts to run higher priority jobs before lower priority jobs\. For more information about job priority, see [Assigning job priority](batch-ops-job-priority.md)\.

1. \(Optional\) Generate a completion report\. To generate select **Generate completion report**\.

   If you choose to generate a completion report, you must choose either to report **Failed tasks only** or **All tasks**, and provide a destination bucket for the report\.

1. Select a valid IAM role\.
**Note**  
For more information about creating a IAM role, see [Configuring IAM policies for Batch Replication](s3-batch-replication-policies.md)\.

1. \(Optional\) Add job tags to the Batch Replication job\.

1. Choose **Next**\.

1. Review your job configuration and select **Create job**\.

## Using the AWS CLI with an S3 manifest<a name="batch-replication-existing-config-cli"></a>

The following example creates an S3 Batch Replication job using a S3 generated manifest for the AWS account `111122223333`\. This example will try to replicate existing objects and objects that previously failed to replicate\. For information about filtering by replication status see, [Specifying a manifest for a Batch Replication job](s3-batch-replication-batch.md#batch-replication-manifest)\.

```
aws s3control create-job --account-id 111122223333 --operation '{"S3ReplicateObject":{}}' --report '{"Bucket":"arn:aws:s3:::*** completion report bucket ****","Prefix":"batch-replication-report", "Format":"Report_CSV_20180820","Enabled":true,"ReportScope":"AllTasks"}' --manifest-generator '{"S3JobManifestGenerator": {"ExpectedBucketOwner": "111122223333", "SourceBucket": "arn:aws:s3:::*** replication source bucket ***", "EnableManifestOutput": false, "Filter": {"EligibleForReplication": true, "ObjectReplicationStatuses": ["NONE","FAILED"]}}}' --priority 1 --role-arn arn:aws:iam::111122223333:role/batch-Replication-IAM-policy --no-confirmation-required --region source-bucket-region
```

**Note**  
The job must be initiated from the same AWS Region replication source bucket\. The IAM role `role/batch-Replication-IAM-policy` was previously created\. See [Configuring IAM policies for Batch Replication](s3-batch-replication-policies.md)\.

After you have successfully initiated a Batch Replication job, you receive the job ID as the response\. You can monitor this job using the following command\.

```
aws s3control describe-job --account-id 111122223333 --job-id job-id --region source-bucket-region
```

## Using the AWS CLI with a user\-provided manifest<a name="batch-replication-existing-config-cli-customer-manifest"></a>

The following example creates an S3 Batch Replication job using a user\-defined manifest for AWS account `111122223333`\. If the objects in your manifest are in a versioned bucket, you must specify the version IDs for the objects\. Only the object with the version ID specified in the maifest will be replicated\. For more information about creating a manifest see, [Specifying a manifest](batch-ops-create-job.md#specify-batchjob-manifest)\.

```
aws s3control create-job --account-id 111122223333 --operation '{"S3ReplicateObject":{}}' --report '{"Bucket":"arn:aws:s3:::*** completion report bucket ****","Prefix":"batch-replication-report", "Format":"Report_CSV_20180820","Enabled":true,"ReportScope":"AllTasks"}' --manifest '{"Spec":{"Format":"S3BatchOperations_CSV_20180820","Fields":["Bucket","Key","VersionId"]},"Location":{"ObjectArn":"arn:aws:s3:::*** completion report bucket ****/manifest.csv","ETag":"Manifest Etag"}}' --priority 1 --role-arn arn:aws:iam::111122223333:role/batch-Replication-IAM-policy --no-confirmation-required --region source-bucket-region
```

**Note**  
The job must be initiated from the same AWS Region replication source bucket\. The IAM role `role/batch-Replication-IAM-policy` was previously created\. See [Configuring IAM policies for Batch Replication](s3-batch-replication-policies.md)\.

After you have successfully initiated a Batch Replication job, you receive the job ID as the response\. You can monitor this job using the following command\.

```
aws s3control describe-job --account-id 111122223333 --job-id job-id --region source-bucket-region
```