# Tracking job status and completion reports<a name="batch-ops-job-status"></a>

With S3 Batch Operations, you can view and update job status, add notifications and logging, track job failures, and generate completion reports\. 

**Topics**
+ [Job statuses](#batch-ops-job-status-table)
+ [Updating job status](#updating-job-statuses)
+ [Notifications and logging](#batch-ops-notifications)
+ [Tracking job failure](#batch-ops-job-status-failure)
+ [Completion reports](#batch-ops-completion-report)
+ [Examples: Tracking an S3 Batch Operations job in Amazon EventBridge through AWS CloudTrail](batch-ops-examples-event-bridge-cloud-trail.md)
+ [Examples: S3 Batch Operations completion reports](batch-ops-examples-reports.md)

## Job statuses<a name="batch-ops-job-status-table"></a>

After you create and run a job, it progresses through a series of statuses\. The following table describes the statuses and the possible transitions between them\. 


****  

| Status | Description | Transitions | 
| --- | --- | --- | 
| New | A job begins in the New state when you create it\. | A job automatically moves to the Preparing state when Amazon S3 begins processing the manifest object\. | 
| Preparing | Amazon S3 is processing the manifest object and other job parameters to set up and run the job\. | A job automatically moves to the Ready state after Amazon S3 finishes processing the manifest and other parameters\. It is then ready to begin running the specified operation on the objects listed in the manifest\.If the job requires confirmation before running, such as when you create a job using the Amazon S3 console, then the job transitions from `Preparing` to `Suspended`\. It remains in the `Suspended` state until you confirm that you want to run it\. | 
| Suspended | The job requires confirmation, but you have not yet confirmed that you want to run it\. Only jobs that you create using the Amazon S3 console require confirmation\. A job that is created using the console enters the Suspended state immediately after Preparing\. After you confirm that you want to run the job and the job becomes Ready, it never returns to the Suspended state\. | After you confirm that you want to run the job, its status changes to Ready\. | 
| Ready | Amazon S3 is ready to begin running the requested object operations\. | A job automatically moves to Active when Amazon S3 begins to run it\. The amount of time that a job remains in the Ready state depends on whether you have higher\-priority jobs running already and how long those jobs take to complete\. | 
| Active | Amazon S3 is performing the requested operation on the objects listed in the manifest\. While a job is Active, you can monitor its progress using the Amazon S3 console or the DescribeJob operation through the REST API, AWS CLI, or AWS SDKs\. | A job moves out of the Active state when it is no longer running operations on objects\. This can happen automatically, such as when a job completes successfully or fails\. Or it can occur as a result of user actions, such as canceling a job\. The state that the job moves to depends on the reason for the transition\. | 
| Pausing | The job is transitioning to Paused from another state\. | A job automatically moves to Paused when the Pausing stage is finished\. | 
| Paused | A job can become Paused if you submit another job with a higher priority while the current job is running\. | A Paused job automatically returns to Active after any higher\-priority jobs that are blocking the job's' execution complete, fail, or are suspended\. | 
| Complete | The job has finished performing the requested operation on all objects in the manifest\. The operation might have succeeded or failed for every object\. If you configured the job to generate a completion report, the report is available as soon as the job is Complete\. | Complete is a terminal state\. Once a job reaches Complete, it does not transition to any other state\. | 
| Cancelling | The job is transitioning to the Cancelled state\. | A job automatically moves to Cancelled when the Cancelling stage is finished\. | 
| Cancelled | You requested that the job be canceled, and S3 Batch Operations has successfully cancelled the job\. The job will not submit any new requests to Amazon S3\. | Cancelled is a terminal state\. After a job reaches Cancelled, it will not transition to any other state\. | 
| Failing | The job is transitioning to the Failed state\. | A job automatically moves to Failed once the Failing stage is finished\. | 
| Failed | The job has failed and is no longer running\. For more information about job failures, see [Tracking job failure](#batch-ops-job-status-failure)\. | Failed is a terminal state\. After a job reaches Failed, it will not transition to any other state\. | 

## Updating job status<a name="updating-job-statuses"></a>

The following AWS CLI and SDK for Java examples update the status of a Batch Operations job\. For more information about using the S3 console to manage Batch Operations jobs, see [Using the Amazon S3 console to manage your S3 Batch Operations jobs](batch-ops-managing-jobs.md#batch-ops-manage-console)\.

### Using the AWS CLI<a name="batch-ops-example-cli-update-job-status"></a>
+ If you didn't specify the `--no-confirmation-required` parameter in the previous `create-job` example, the job remains in a suspended state until you confirm the job by setting its status to `Ready`\. Amazon S3 then makes the job eligible for execution\.

  ```
  aws s3control update-job-status \
      --region us-west-2 \
      --account-id 181572960644 \
      --job-id 00e123a4-c0d8-41f4-a0eb-b46f9ba5b07c \
      --requested-job-status 'Ready'
  ```

  
+ Cancel the job by setting the job status to `Cancelled`\.

  ```
  aws s3control update-job-status \
       --region us-west-2 \
       --account-id 181572960644 \
       --job-id 00e123a4-c0d8-41f4-a0eb-b46f9ba5b07c \
       --status-update-reason "No longer needed" \
       --requested-job-status Cancelled
  ```

  

### Using the AWS SDK for Java<a name="batch-ops-examples-java-update-job-status"></a>

The following example updates the status of an S3 Batch Operations job using the AWS SDK for Java\.

For more information about job status, see [Tracking job status and completion reports](#batch-ops-job-status)\.

**Example**  

```
package aws.example.s3control;


import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.services.s3control.AWSS3Control;
import com.amazonaws.services.s3control.AWSS3ControlClient;
import com.amazonaws.services.s3control.model.UpdateJobStatusRequest;

import static com.amazonaws.regions.Regions.US_WEST_2;

public class UpdateJobStatus {
    public static void main(String[] args) {
        String accountId = "Account ID";
        String jobId = "00e123a4-c0d8-41f4-a0eb-b46f9ba5b07c";

        try {
            AWSS3Control s3ControlClient = AWSS3ControlClient.builder()
                    .withCredentials(new ProfileCredentialsProvider())
                    .withRegion(US_WEST_2)
                    .build();

            s3ControlClient.updateJobStatus(new UpdateJobStatusRequest()
                    .withAccountId(accountId)
                    .withJobId(jobId)
                    .withRequestedJobStatus("Ready"));

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

## Notifications and logging<a name="batch-ops-notifications"></a>

In addition to requesting completion reports, you can also capture, review, and audit Batch Operations activity using AWS CloudTrail\. Because Batch Operations use existing Amazon S3 APIs to perform tasks, those tasks also emit the same events that they would if you called them directly\. Thus, you can track and record the progress of your job and all of its tasks using the same notification, logging, and auditing tools and processes that you already use with Amazon S3\. For more information, see the examples in the following sections\.

**Note**  
Amazon S3 Batch Operations generates both management and data events in CloudTrail during job execution\. The volume of these events scale with the number of keys in each job’s manifest\. Refer to the [CloudTrail pricing](http://aws.amazon.com/cloudtrail/pricing/) page for details, which includes examples of how pricing changes depending on the number of trails you have configured in your account\. To learn how to configure and log events to fit your needs, see [Create your first trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-tutorial.html#tutorial-step2) in the *AWS CloudTrail User Guide*\.

For more information about Amazon S3 events, see [Amazon S3 Event Notifications](EventNotifications.md)\. 

## Tracking job failure<a name="batch-ops-job-status-failure"></a>

If an S3 Batch Operations job encounters a problem that prevents it from running successfully, such as not being able to read the specified manifest, the job fails\. When a job fails, it generates one or more failure codes or failure reasons\. S3 Batch Operations store the failure codes and reasons with the job so that you can view them by requesting the job's details\. If you requested a completion report for the job, the failure codes and reasons also appear there\.

To prevent jobs from running a large number of unsuccessful operations, Amazon S3 imposes a task\-failure threshold on every Batch Operations job\. When a job has run at least 1,000 tasks, Amazon S3 monitors the task failure rate\. At any point, if the failure rate \(the number of tasks that have failed as a proportion of the total number of tasks that have run\) exceeds 50 percent, the job fails\. If your job fails because it exceeded the task\-failure threshold, you can identify the cause of the failures\. For example, you might have accidentally included some objects in the manifest that don't exist in the specified bucket\. After fixing the errors, you can resubmit the job\.

**Note**  
S3 Batch Operations operate asynchronously and the tasks don't necessarily run in the order that the objects are listed in the manifest\. Therefore, you can't use the manifest ordering to determine which objects' tasks succeeded and which ones failed\. Instead, you can examine the job's completion report \(if you requested one\) or view your AWS CloudTrail event logs to help determine the source of the failures\.

## Completion reports<a name="batch-ops-completion-report"></a>

When you create a job, you can request a completion report\. As long as S3 Batch Operations successfully invoke at least one task, Amazon S3 generates a completion report after it finishes running tasks, fails, or is canceled\. You can configure the completion report to include all tasks or only failed tasks\. 

The completion report includes the job configuration and status and information for each task, including the object key and version, status, error codes, and descriptions of any errors\. Completion reports provide an easy way to view the results of your tasks in a consolidated format with no additional setup required\. For an example of a completion report, see [Examples: S3 Batch Operations completion reports](batch-ops-examples-reports.md)\. 

If you don't configure a completion report, you can still monitor and audit your job and its tasks using CloudTrail and Amazon CloudWatch\. For more information, see the following section\.

**Topics**
+ [Job statuses](#batch-ops-job-status-table)
+ [Updating job status](#updating-job-statuses)
+ [Notifications and logging](#batch-ops-notifications)
+ [Tracking job failure](#batch-ops-job-status-failure)
+ [Completion reports](#batch-ops-completion-report)
+ [Examples: Tracking an S3 Batch Operations job in Amazon EventBridge through AWS CloudTrail](batch-ops-examples-event-bridge-cloud-trail.md)
+ [Examples: S3 Batch Operations completion reports](batch-ops-examples-reports.md)