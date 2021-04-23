# Tracking job status<a name="batch-ops-job-status"></a>

After you create a job, it progresses through a series of statuses\. The following table describes the statuses that jobs can have and the possible transitions between job statuses\.


****  

| Status | Description | Transitions | 
| --- | --- | --- | 
| New | A job begins in the New state when you create it\. | A job automatically moves to the Preparing state when Amazon S3 begins processing the manifest object\. | 
| Preparing | Amazon S3 is processing the manifest object and other job parameters to set up and run the job\. | A job automatically moves to the Ready state after Amazon S3 finishes processing the manifest and other parameters\. It is then ready to begin running the specified operation on the objects listed in the manifest\.If the job requires confirmation before running, such as when you create a job using the Amazon S3 console, then the job transitions from `Preparing` to `Suspended`\. It remains in the `Suspended` state until you confirm that you want to run it\. | 
| Suspended | The job requires confirmation, but you have not yet confirmed that you want to run it\. Only jobs that you create using the Amazon S3 console require confirmation\. A job that is created using the console enters the Suspended state immediately after Preparing\. After you confirm that you want to run the job and the job becomes Ready, it never returns to the Suspended state\. | After you confirm that you want to run the job, its status changes to Ready\. | 
| Ready | Amazon S3 is ready to begin running the requested object operations\. | A job automatically moves to Active when Amazon S3 begins to run it\. The amount of time that a job remains in the Ready state depends on whether you have higher\-priority jobs running already and how long those jobs take to complete\. | 
| Active | Amazon S3 is executing the requested operation on the objects listed in the manifest\. While a job is Active, you can monitor its progress using the Amazon S3 console or the DescribeJob operation through the REST API, AWS CLI, or AWS SDKs\. | A job moves out of the Active state when it is no longer running operations on objects\. This can happen automatically, such as when a job completes successfully or fails\. Or it can occur as a result of user actions, such as canceling a job\. The state that the job moves to depends on the reason for the transition\. | 
| Pausing | The job is transitioning to Paused from another state\. | A job automatically moves to Paused when the Pausing stage is finished\. | 
| Paused | A job can become Paused if you submit another job with a higher priority while the current job is running\. | A Paused job automatically returns to Active after any higher\-priority jobs that are blocking the job's' execution complete, fail, or are suspended\. | 
| Complete | The job has finished executing the requested operation on all objects in the manifest\. The operation might have succeeded or failed for every object\. If you configured the job to generate a completion report, the report is available as soon as the job is Complete\. | Complete is a terminal state\. Once a job reaches Complete, it does not transition to any other state\. | 
| Cancelling | The job is transitioning to the Cancelled state\. | A job automatically moves to Cancelled when the Cancelling stage is finished\. | 
| Cancelled | You requested that the job be cancelled, and S3 Batch Operations has successfully cancelled the job\. The job will not submit any new requests to Amazon S3\. | Cancelled is a terminal state\. After a job reaches Cancelled, it will not transition to any other state\. | 
| Failing | The job is transitioning to the Failed state\. | A job automatically moves to Failed once the Failing stage is finished\. | 
| Failed | The job has failed and is no longer running\. For more information about job failures, see [Tracking job failure](batch-ops-managing-jobs.md#batch-ops-job-status-failure)\. | Failed is a terminal state\. After a job reaches Failed, it will not transition to any other state\. | 

## Updating the job status<a name="updating-job-statuses"></a>

These examples show you how to update the status of a Batch Operations job using the AWS CLI and the SDK for Java\. For more information about using the S3 console to manage Batch Operations jobs, see [Managing S3 Batch Operations jobs using the S3 console](batch-ops-manage-jobs.md)

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

  

### Using the AWS SDK Java job<a name="batch-ops-examples-java-update-job-status"></a>

The following example updates the status of an S3 Batch Operations job using the AWS SDK for Java\.

For more information about job status, see [Tracking job status](#batch-ops-job-status)\.

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