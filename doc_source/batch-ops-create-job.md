--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Creating an S3 Batch Operations job<a name="batch-ops-create-job"></a>

With S3 Batch Operations, you can perform large\-scale Batch Operations on a list of specific Amazon S3 objects\. You can create S3 Batch Operations jobs using the AWS Management Console, AWS Command Line Interface \(AWS CLI\), AWS SDKs, or REST API\. 

For more information about Batch Operations, see [S3 Batch Operations basics](batch-ops-basics.md)\.

This section describes the information that you need to create an S3 Batch Operations job and the results of a `Create Job` request\. It also provides instructions for creating a Batch Operations job\.

When you create an S3 Batch Operations job, you can request a completion report for all tasks or just for failed tasks\. As long as at least one task has been invoked successfully, S3 Batch Operations generates a report for jobs that have completed, failed, or been canceled\. For more information, see [S3 Batch Operations completion reports](batch-ops-examples-reports.md)\.

**Prerequisite: **Before you create a Batch Operations job, confirm that you have configured relevant permissions\. For more information, see [Granting permissions for Amazon S3 Batch Operations](batch-ops-iam-role-policies.md)\.

## Using the S3 console<a name="batch-ops-create-job-console"></a>

This section describes how to create a S3 Batch Operations job\. using the Amazon S3 console\. For more information about the details of creating a job request, see [Batch Operations request elements](batch-ops-basics.md#batch-ops-create-job-request-elements)\.

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

## Using the AWS CLI<a name="batch-ops-example-cli-job-create"></a>

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

## Using the AWS SDK for Java<a name="batch-ops-examples-java-create-job"></a>

The following example creates an S3 Batch Operations job using the AWS SDK for Java\.

For information about setting up the permissions that you need to create a job, see [Granting permissions for Amazon S3 Batch Operations](batch-ops-iam-role-policies.md)\. 

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

## Related resources<a name="batch-ops-create-job-related-resources"></a>
+ [S3 Batch Operations basics](batch-ops-basics.md)
+ [Operations](batch-ops-operations.md)
+ [Managing S3 Batch Operations jobs](batch-ops-managing-jobs.md)