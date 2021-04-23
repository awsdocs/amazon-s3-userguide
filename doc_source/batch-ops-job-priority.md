# Assigning job priority<a name="batch-ops-job-priority"></a>

You can assign each job a numeric priority, which can be any positive integer\. S3 Batch Operations prioritize jobs according to the assigned priority\. Jobs with a higher priority \(or a higher numeric value for the priority parameter\) are evaluated first\. Priority is determined in descending order\. For example, a job queue with a priority value of 10 is given scheduling preference over a job queue with a priority value of 1\. 

You can change a job's priority while it is running\. If you submit a new job with a higher priority while a job is running, the lower\-priority job can pause to allow the higher\-priority job to run\.

**Note**  
S3 Batch Operations honor job priorities on a best\-effort basis\. Although jobs with higher priorities generally take precedence over jobs with lower priorities, Amazon S3 does not guarantee strict ordering of jobs\.

## Using the AWS CLI<a name="batch-ops-example-cli-update-job-priority"></a>

The following example updates the job priority using the AWS CLI\. A higher number indicates a higher execution priority\.

```
aws s3control update-job-priority \
    --region us-west-2 \
    --account-id acct-id \
    --priority 98 \
    --job-id 00e123a4-c0d8-41f4-a0eb-b46f9ba5b07c
```



## Using the AWS SDK for Java<a name="batch-ops-examples-java-update-job-priority."></a>

The following example updates the priority of an S3 Batch Operations job using the AWS SDK for Java\.

For more information about job priority, see [Assigning job priority](#batch-ops-job-priority)\.

**Example**  

```
package aws.example.s3control;



import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.services.s3control.AWSS3Control;
import com.amazonaws.services.s3control.AWSS3ControlClient;
import com.amazonaws.services.s3control.model.UpdateJobPriorityRequest;

import static com.amazonaws.regions.Regions.US_WEST_2;

public class UpdateJobPriority {
    public static void main(String[] args) {
        String accountId = "Account ID";
        String jobId = "00e123a4-c0d8-41f4-a0eb-b46f9ba5b07c";

        try {
            AWSS3Control s3ControlClient = AWSS3ControlClient.builder()
                    .withCredentials(new ProfileCredentialsProvider())
                    .withRegion(US_WEST_2)
                    .build();

            s3ControlClient.updateJobPriority(new UpdateJobPriorityRequest()
                    .withAccountId(accountId)
                    .withJobId(jobId)
                    .withPriority(98));

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