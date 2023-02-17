# Creating an S3 Batch Operations job<a name="batch-ops-create-job"></a>

 

With S3 Batch Operations, you can perform large\-scale batch operations on a list of specific Amazon S3 objects\. This section describes the information that you need to create an S3 Batch Operations job and the results of a `Create Job` request\. It also provides instructions for creating a Batch Operations job using the AWS Management Console, AWS Command Line Interface \(AWS CLI\), and AWS SDK for Java\.

When you create an S3 Batch Operations job, you can request a completion report for all tasks or only for failed tasks\. As long as at least one task has been invoked successfully, S3 Batch Operations generates a report for jobs that have completed, failed, or been canceled\. For more information, see [Examples: S3 Batch Operations completion reports](batch-ops-examples-reports.md)\.

The following video provides a brief demonstration of how to create a Batch Operations job using the AWS Management Console\.

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/hUv34voEftc//0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/hUv34voEftc/)

**Topics**
+ [Batch Operations job request elements](#batch-ops-create-job-request-elements)
+ [Specifying a manifest](#specify-batchjob-manifest)
+ [Creating a job](#to-create-batch-ops-job)
+ [Job responses](#batch-ops-create-job-response-elements)

## Batch Operations job request elements<a name="batch-ops-create-job-request-elements"></a>

To create an S3 Batch Operations job, you must provide the following information:

**Operation**  
Specify the operation that you want S3 Batch Operations to run against the objects in the manifest\. Each operation type accepts parameters that are specific to that operation\. This enables you to perform the same tasks as if you performed the operation one\-by\-one on each object\.

**Manifest**  
The manifest is a list of all of the objects that you want S3 Batch Operations to run the specified action on\. You can use a CSV\-formatted [Amazon S3 Inventory](storage-inventory.md) report as a manifest or use your own customized CSV list of objects\.   
If the objects in your manifest are in a versioned bucket, specifying the version IDs for the objects will perform the operation on a specific version\. Batch Operations will perform the operation on the latest version if no version ID is specified\. If your manifest includes a version ID field, you must provide a version ID for all objects in the manifest\. For more information, see [Specifying a manifest](#specify-batchjob-manifest)\.

**Priority**  
Use job priorities to indicate the relative priority of this job to others running in your account\. A higher number indicates higher priority\.  
Job priorities only have meaning relative to the priorities that are set for other jobs in the same account and Region\. You can choose whatever numbering system works for you\. For example, you might want to assign all `Initiate Restore Object` jobs a priority of 1, all `PUT Object Copy` jobs a priority of 2, and all `Put Object ACL` jobs a priority of 3\.   
S3 Batch Operations prioritize jobs according to priority numbers, but strict ordering isn't guaranteed\. Therefore, you shouldn't use job priorities to ensure that any one job starts or finishes before any other job\. If you need to ensure strict ordering, wait until one job has finished before starting the next\. 

**RoleArn**  
Specify an AWS Identity and Access Management \(IAM\) role to run the job\. The IAM role that you use must have sufficient permissions to perform the operation that is specified in the job\. For example, to run a `PUT Object Copy` job, the IAM role must have `s3:GetObject` permissions for the source bucket and `s3:PutObject` permissions for the destination bucket\. The role also needs permissions to read the manifest and write the job\-completion report\.   
For more information about IAM roles, see [IAM Roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) in the *IAM User Guide*\.  
For more information about Amazon S3 permissions, see [Amazon S3 actions](using-with-s3-actions.md)\.

**Report**  
Specify whether you want S3 Batch Operations to generate a completion report\. If you request a job\-completion report, you must also provide the parameters for the report in this element\. The necessary information includes:  
+ The bucket where you want to store the report
+ The format of the report
+ Whether you want the report to include the details of all tasks or only failed tasks
+ An optional prefix string

**Tags \(optional\)**  
You can label and control access to your S3 Batch Operations jobs by adding *tags*\. Tags can be used to identify who is responsible for a Batch Operations job\. You can create jobs with tags attached to them, and you can add tags to jobs after you create them\. For example, you could grant a user permission to invoke `CreateJob` provided that the job is created with the tag `"Department=Finance"`\.   
For more information, see [Controlling access and labeling jobs using tags](batch-ops-job-tags.md)\.

**Description \(optional\)**  
To track and monitor your job, you can also provide a description of up to 256 characters\. Amazon S3 includes this description whenever it returns information about a job or displays job details on the Amazon S3 console\. You can then easily sort and filter jobs according to the descriptions that you assigned\. Descriptions don't need to be unique, so you can use descriptions as categories \(for example, "Weekly Log Copy Jobs"\) to help you track groups of similar jobs\.

## Specifying a manifest<a name="specify-batchjob-manifest"></a>

 A manifest is an Amazon S3 object that contains object keys that you want Amazon S3 to act upon\. To create a manifest for a job, you specify the manifest object key, ETag, and optional version ID\. The contents of the manifest must be URL encoded\. Manifests that use server\-side encryption with customer\-provided keys \(SSE\-C\) are not supported\. Manifests that use server\-side encryption with AWS Key Management Service \(SSE\-KMS\) AWS KMS keys are only supported when using CSV\-formatted inventory reports\. Your manifest must contain the bucket name, object key, and optionally, the object version for each object\. Any other fields in the manifest are not used by S3 Batch Operations\. 

You can specify a manifest in a create job request using one of the following two formats\.
+ Amazon S3 Inventory report — Must be a CSV\-formatted Amazon S3 Inventory report\. You must specify the `manifest.json` file that is associated with the inventory report\. For more information about inventory reports, see [Amazon S3 Inventory](storage-inventory.md)\. If the inventory report includes version IDs, S3 Batch Operations operates on the specific object versions\.
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
When using a user supplied manifest and a versioned bucket, we recommend that you specify the version IDs for the objects\. When you create a job, S3 Batch Operations parses the entire manifest before running the job\. However, it doesn't take a "snapshot" of the state of the bucket\.   
Because manifests can contain billions of objects, jobs might take a long time to run\. If you overwrite an object with a new version while a job is running and you didn't specify a version ID for that object, Amazon S3 performs the operation on the latest version of the object, not on the version that existed when you created the job\. The only way to avoid this behavior is to specify version IDs for the objects that are listed in the manifest\. 

**Note**  
Amazon S3 gives you the option to create a manifest for the S3 Batch Replication job\. Batch Replication is an on\-demand operation that replicates existing objects\. For more information about Batch Replication, see [Replicating existing objects with S3 Batch Replication](s3-batch-replication-batch.md)\.

## Creating a job<a name="to-create-batch-ops-job"></a>

 You can create S3 Batch Operations jobs using the AWS Management Console, AWS CLI, Amazon SDKs, or REST API\. 

For more information about creating a job request, see [Batch Operations job request elements](#batch-ops-create-job-request-elements)\. 

**Prerequisite**  
Before you create a Batch Operations job, confirm that you have configured relevant permissions\. For more information, see [Granting permissions for Amazon S3 Batch Operations](batch-ops-iam-role-policies.md)\.

### Using the S3 console<a name="batch-ops-create-job-console"></a>

**To create a batch job**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose **Batch Operations** on the navigation pane of the Amazon S3 console\.

1. Choose **Create job**\.

1. Choose the **Region** where you want to create your job\.

1. Under **Manifest format**, choose the type of manifest object to use\.
   + If you choose **S3 inventory report**, enter the path to the manifest\.json object that Amazon S3 generated as part of the CSV\-formatted Inventory report, and optionally the version ID for the manifest object if you want to use a version other than the most recent\.
   + If you choose **CSV**, enter the path to a CSV\-formatted manifest object\. The manifest object must follow the format described in the console\. You can optionally include the version ID for the manifest object if you want to use a version other than the most recent\.

1. Choose **Next**\.

1. Under **Operation**, choose the operation that you want to perform on all objects listed in the manifest\. Fill out the information for the operation you chose and then choose **Next**\.

1. Fill out the information for **Configure additional options** and then choose **Next**\.

1. For **Review**, verify the settings\. If you need to make changes, choose **Previous**\. Otherwise, choose **Create Job**\.

### Using the AWS CLI<a name="batch-ops-example-cli-job-create"></a>

The following example creates an S3 Batch Operations `S3PutObjectTagging` job using the AWS CLI\. 

**To create a Batch Operations `S3PutObjectTagging` job**

1. Create an AWS Identity and Access Management \(IAM\) role, and assign permissions\. This role grants Amazon S3 permission to add object tags, for which you create a job in the next step\.

   1. Create an IAM role as follows\.

      ```
      aws iam create-role \
       --role-name S3BatchJobRole \
       --assume-role-policy-document '{
         "Version":"2012-10-17",
         "Statement":[
            {
               "Effect":"Allow",
               "Principal":{
                  "Service":"batchoperations.s3.amazonaws.com"
               },
               "Action":"sts:AssumeRole"
            }
         ]
      }'
      ```

      Record the role's Amazon Resource Name \(ARN\)\. You need the ARN when you create a job\.

   1. Create an IAM policy with permissions, and attach it to the IAM role that you created in the previous step\. For more information about permissions, see [Granting permissions for Amazon S3 Batch Operations](batch-ops-iam-role-policies.md)\.

      ```
      aws iam put-role-policy \
        --role-name S3BatchJobRole \
        --policy-name PutObjectTaggingBatchJobPolicy \
        --policy-document '{
        "Version":"2012-10-17",
        "Statement":[
          {
            "Effect":"Allow",
            "Action":[
              "s3:PutObjectTagging",
              "s3:PutObjectVersionTagging"
            ],
            "Resource": "arn:aws:s3:::{{TargetResource}}/*"
          },
          {
            "Effect": "Allow",
            "Action": [
              "s3:GetObject",
              "s3:GetObjectVersion",
              "s3:GetBucketLocation"
            ],
            "Resource": [
              "arn:aws:s3:::{{ManifestBucket}}",
              "arn:aws:s3:::{{ManifestBucket}}/*"
            ]
          },
          {
            "Effect":"Allow",
            "Action":[
              "s3:PutObject",
              "s3:GetBucketLocation"
            ],
            "Resource":[
              "arn:aws:s3:::{{ReportBucket}}",
              "arn:aws:s3:::{{ReportBucket}}/*"
            ]
          }
        ]
      }'
      ```

      

1. Create an `S3PutObjectTagging` job\. 

   The `manifest.csv` file provides a list of bucket and object key values\. The job applies the specified tags to objects identified in the manifest\. The `ETag` is the ETag of the `manifest.csv` object, which you can get from the Amazon S3 console\. The request specifies the `no-confirmation-required` parameter\. Therefore, Amazon S3 makes the job eligible for execution without you having to confirm it using the `udpate-job-status` command\.

   ```
   aws s3control create-job \
       --region us-west-2 \
       --account-id acct-id \
       --operation '{"S3PutObjectTagging": { "TagSet": [{"Key":"keyOne", "Value":"ValueOne"}] }}' \
       --manifest '{"Spec":{"Format":"S3BatchOperations_CSV_20180820","Fields":["Bucket","Key"]},"Location":{"ObjectArn":"arn:aws:s3:::my_manifests/manifest.csv","ETag":"60e460c9d1046e73f7dde5043ac3ae85"}}' \
       --report '{"Bucket":"arn:aws:s3:::bucket-where-completion-report-goes","Prefix":"final-reports", "Format":"Report_CSV_20180820","Enabled":true,"ReportScope":"AllTasks"}' \
       --priority 42 \
       --role-arn IAM-role \
       --client-request-token $(uuidgen) \
       --description "job Description" \
       --no-confirmation-required
   ```

   In response, Amazon S3 returns a job ID \(for example, `00e123a4-c0d8-41f4-a0eb-b46f9ba5b07c`\)\. You need the ID in the next commands\.

### Using the AWS SDK for Java<a name="batch-ops-examples-java-create-job"></a>

The following example creates an S3 Batch Operations job using the AWS SDK for Java\.

**Example**  

```
package aws.example.s3control;



import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.services.s3control.AWSS3Control;
import com.amazonaws.services.s3control.AWSS3ControlClient;
import com.amazonaws.services.s3control.model.*;

import java.util.UUID;
import java.util.ArrayList;

import static com.amazonaws.regions.Regions.US_WEST_2;

public class CreateJob {
    public static void main(String[] args) {
        String accountId = "Account ID";
        String iamRoleArn = "IAM Role ARN";
        String reportBucketName = "arn:aws:s3:::bucket-where-completion-report-goes";
        String uuid = UUID.randomUUID().toString();

        ArrayList tagSet = new ArrayList<S3Tag>();
        tagSet.add(new S3Tag().withKey("keyOne").withValue("ValueOne"));


        try {
            JobOperation jobOperation = new JobOperation()
                    .withS3PutObjectTagging(new S3SetObjectTaggingOperation()
                            .withTagSet(tagSet)
                    );

            JobManifest manifest = new JobManifest()
                    .withSpec(new JobManifestSpec()
                            .withFormat("S3BatchOperations_CSV_20180820")
                            .withFields(new String[]{
                                    "Bucket", "Key"
                            }))
                    .withLocation(new JobManifestLocation()
                            .withObjectArn("arn:aws:s3:::my_manifests/manifest.csv")
                            .withETag("60e460c9d1046e73f7dde5043ac3ae85"));
            JobReport jobReport = new JobReport()
                    .withBucket(reportBucketName)
                    .withPrefix("reports")
                    .withFormat("Report_CSV_20180820")
                    .withEnabled(true)
                    .withReportScope("AllTasks");

            AWSS3Control s3ControlClient = AWSS3ControlClient.builder()
                    .withCredentials(new ProfileCredentialsProvider())
                    .withRegion(US_WEST_2)
                    .build();

            s3ControlClient.createJob(new CreateJobRequest()
                    .withAccountId(accountId)
                    .withOperation(jobOperation)
                    .withManifest(manifest)
                    .withReport(jobReport)
                    .withPriority(42)
                    .withRoleArn(iamRoleArn)
                    .withClientRequestToken(uuid)
                    .withDescription("job description")
                    .withConfirmationRequired(false)
            );

        } catch (AmazonServiceException e) {
            // The call was transmitted successfully, but Amazon S3 couldn't process
            // it and returned an error response.
            e.printStackTrace();
        } catch (SdkClientException e) {
            // Amazon S3 couldn't be contacted for a response, or the client
            // couldn't parse the response from Amazon S3.
            e.printStackTrace();
        }
    }
}
```

### Using the REST API<a name="batch-ops-examples-rest-create-job"></a>

You can use the REST API to create a Batch Operations job\. For more information, see [CreateJob REST API](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_CreateJob.html) in the *Amazon Simple Storage Service API Reference*\. 

## Job responses<a name="batch-ops-create-job-response-elements"></a>

If the `Create Job` request succeeds, Amazon S3 returns a job ID\. The job ID is a unique identifier that Amazon S3 generates automatically so that you can identify your Batch Operations job and monitor its status\.

When you create a job through the AWS CLI, Amazon SDKs, or REST API, you can set S3 Batch Operations to begin processing the job automatically\. The job runs as soon as it's ready and not waiting behind higher\-priority jobs\. 

When you create a job through the AWS Management Console, you must review the job details and confirm that you want to run it before Batch Operations can begin to process it\. After you confirm that you want to run the job, it progresses as though you created it through one of the other methods\. If a job remains in the suspended state for over 30 days, it will fail\.