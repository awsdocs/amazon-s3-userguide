--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# S3 Batch Operations basics<a name="batch-ops-basics"></a>

You can use S3 Batch Operations to perform large\-scale batch operations on Amazon S3 objects\. S3 Batch Operations can run a single operation or action on lists of Amazon S3 objects that you specify\. 

**Topics**
+ [How an S3 Batch Operations job works](#batch-ops-basics-how-it-works)
+ [Specifying a manifest](#specify-batchjob-manifest)
+ [Batch Operations request elements](#batch-ops-create-job-request-elements)
+ [Job responses](#batch-ops-create-job-response-elements)

## How an S3 Batch Operations job works<a name="batch-ops-basics-how-it-works"></a>

A job is the basic unit of work for S3 Batch Operations\. A job contains all of the information necessary to run the specified operation on a list of objects\. To create a job, you give S3 Batch Operations a list of objects and specify the action to perform on those objects\. 

S3 Batch Operations supports the following operations:
+ [Put object copy](batch-ops-copy-object.md)
+ [Initiate restore object](batch-ops-initiate-restore-object.md)
+ [Invoke Lambda function](batch-ops-invoke-lambda.md)
+ [Put object ACL](batch-ops-put-object-acl.md)
+ [Put object tagging](batch-ops-put-object-tagging.md)
+ [Object Lock retention date](batch-ops-retention-date.md)
+ [Object Lock legal hold](batch-ops-legal-hold.md)

A batch job performs a specified operation on each object that is included in its *manifest*\. A manifest lists the objects that you want a batch job to process and it is stored as an object in a bucket\. You can use a CSV\-formatted [ Amazon S3 inventory](storage-inventory.md) report as a manifest, which makes it easy to create large lists of objects located in a bucket\. You can also specify a manifest in a simple CSV format that enables you to perform batch operations on a customized list of objects contained within a single bucket\. 

After you create a job, Amazon S3 processes the list of objects in the manifest and runs the specified operation against each object\. While a job is executing, you can monitor its progress programmatically or through the Amazon S3 console\. You can also configure a job to generate a completion report when it finishes\. The completion report describes the results of each task that was performed by the job\. For more information about monitoring jobs, see [Managing S3 Batch Operations jobs](batch-ops-managing-jobs.md)\.

## Specifying a manifest<a name="specify-batchjob-manifest"></a>

 A manifest is an Amazon S3 object that lists object keys that you want Amazon S3 to act upon\. To create a manifest for a job, you specify the manifest object key, ETag, and optional version ID\. The contents of the manifest must be URL encoded\. Manifests that use server\-side encryption with customer\-provided keys \(SSE\-C\) and server\-side encryption with AWS Key Management Service \(SSE\-KMS\) customer master keys \(CMKs\) are not supported\. Your manifest must contain the bucket name, object key, and optionally, the object version for each object\. Any other fields in the manifest are not used by S3 Batch Operations\. 

You can specify a manifest in a create job request using one of the following two formats\.
+ Amazon S3 inventory report — Must be a CSV\-formatted Amazon S3 inventory report\. You must specify the `manifest.json` file that is associated with the inventory report\. For more information about inventory reports, see [ Amazon S3 inventory](storage-inventory.md)\. If the inventory report includes version IDs, S3 Batch Operations operates on the specific object versions\.
**Note**  
S3 Batch Operations supports CSV *inventory reports *that are AWS KMS\-encrypted\.
+ CSV file — Each row in the file must include the bucket name, object key, and optionally, the object version\. Object keys must be URL\-encoded, as shown in the following examples\. The manifest must either include version IDs for all objects or omit version IDs for all objects\. For more information about the CSV manifest format, see [JobManifestSpec](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_JobManifestSpec.html) in the *Amazon Simple Storage Service API Reference*\.
**Note**  
S3 Batch Operations does not support CSV *manifest files* that are AWS KMS\-encrypted\.

  The following is an example manifest in CSV format without version IDs\.

  ```
  Examplebucket,objectkey1
  Examplebucket,objectkey2
  Examplebucket,objectkey3
  Examplebucket,photos/jpgs/objectkey4
  Examplebucket,photos/jpgs/newjersey/objectkey5
  Examplebucket,object%20key%20with%20spaces
  ```

  The following is an example manifest in CSV format including version IDs\.

  ```
  Examplebucket,objectkey1,PZ9ibn9D5lP6p298B7S9_ceqx1n5EJ0p
  Examplebucket,objectkey2,YY_ouuAJByNW1LRBfFMfxMge7XQWxMBF
  Examplebucket,objectkey3,jbo9_jhdPEyB4RrmOxWS0kU0EoNrU_oI
  Examplebucket,photos/jpgs/objectkey4,6EqlikJJxLTsHsnbZbSRffn24_eh5Ny4
  Examplebucket,photos/jpgs/newjersey/objectkey5,imHf3FAiRsvBW_EHB8GOu.NHunHO1gVs
  Examplebucket,object%20key%20with%20spaces,9HkPvDaZY5MVbMhn6TMn1YTb5ArQAo3w
  ```

**Important**  
If the objects in your manifest are in a versioned bucket, you should specify the version IDs for the objects\. When you create a job, S3 Batch Operations parses the entire manifest before running the job\. However, it doesn't take a "snapshot" of the state of the bucket\.   
Because manifests can contain billions of objects, jobs might take a long time to run\. If you overwrite an object with a new version while a job is running, and you didn't specify a version ID for that object, Amazon S3 performs the operation on the latest version of the object, and not the version that existed when you created the job\. The only way to avoid this behavior is to specify version IDs for the objects that are listed in the manifest\. 

## Batch Operations request elements<a name="batch-ops-create-job-request-elements"></a>

To create an S3 Batch Operations job, you must provide the following information:

**Operation**  
Specify the operation that you want S3 Batch Operations to run against the objects in the manifest\. Each operation type accepts parameters that are specific to that operation\. This enables you to perform the same tasks as if you performed the operation one\-by\-one on each object\.

**Manifest**  
The manifest is a list of all of the objects that you want S3 Batch Operations to run the specified action on\. You can use a CSV\-formatted [ Amazon S3 inventory](storage-inventory.md) report as a manifest or use your own customized CSV list of objects\.   
For more information about manifests, see [Specifying a manifest](#specify-batchjob-manifest)\.

**Priority**  
Use job priorities to indicate the relative priority of this job to others running in your account\. A higher number indicates higher priority\.  
 Job priorities only have meaning relative to the priorities that are set for other jobs in the same account and Region\. So you can choose whatever numbering system works for you\. For example, you might want to assign all `Initiate Restore Object` jobs a priority of 1, all `PUT Object Copy` jobs a priority of 2, and all `Put Object ACL` jobs a priority of 3\.   
S3 Batch Operations prioritize jobs according to priority numbers, but strict ordering isn't guaranteed\. Thus, you shouldn't use job priorities to ensure that any one job will start or finish before any other job\. If you need to ensure strict ordering, wait until one job has finished before starting the next\. 

**RoleArn**  
Specify an AWS Identity and Access Management \(IAM\) role to run the job\. The IAM role that you use must have sufficient permissions to perform the operation that is specified in the job\. For example, to run a `PUT Object Copy` job, the IAM role must have `s3:GetObject` permissions for the source bucket and `s3:PutObject` permissions for the destination bucket\. The role also needs permissions to read the manifest and write the job\-completion report\.   
For more information about IAM roles, see [IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) in the *IAM User Guide*\.   
For more information about Amazon S3 permissions, see [Amazon S3 actions](using-with-s3-actions.md)\.

**Report**  
Specify whether you want S3 Batch Operations to generate a completion report\. If you request a job\-completion report, you must also provide the parameters for the report in this element\. The necessary information includes the bucket where you want to store the report, the format of the report, whether you want the report to include the details of all tasks or only failed tasks, and an optional prefix string\.

**Tags \(Optional\)**  
You can label and control access to your S3 Batch Operations jobs by adding *tags*\. Tags can be used to identify who is responsible for a Batch Operations job\. You can create jobs with tags attached to them, and you can add tags to jobs after you create them\. For example, you could grant an IAM user permission to invoke `CreateJob` provided that the job is created with the tag `"Department=Finance"`\.   
For more information, see [Controlling access and labeling jobs using tags](batch-ops-job-tags.md)\.

**Description \(Optional\)**  
To track and monitor your job, you can also provide a description of up to 256 characters\. Amazon S3 includes this description whenever it returns information about a job or displays job details on the Amazon S3 console\. You can then easily sort and filter jobs according to the descriptions that you assigned\. Descriptions don't need to be unique, so you can use descriptions as categories \(for example, "Weekly Log Copy Jobs"\) to help you track groups of similar jobs\.

## Job responses<a name="batch-ops-create-job-response-elements"></a>

If the `Create Job` request succeeds, Amazon S3 returns a job ID\. The job ID is a unique identifier that Amazon S3 generates automatically so that you can identify your Batch Operations job and monitor its status\.

When you create a job through the AWS CLI, AWS SDKs, or REST API, you can set S3 Batch Operations to begin processing the job automatically\. The job runs as soon as it's ready and not waiting behind higher\-priority jobs\. 

When you create a job through the AWS Management Console, you must review the job details and confirm that you want to run it before Batch Operations can begin to process it\. After you confirm that you want to run the job, it progresses as though you had created it through one of the other methods\. If a job remains in the suspended state for over 30 days, it will fail\.