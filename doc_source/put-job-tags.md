# Putting job tags for an existing S3 Batch Operations job<a name="put-job-tags"></a>

You can use [PutJobTagging](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutJobTagging.html) to add job tags to your existing S3 Batch Operations jobs\. For more information, see the following examples\.

## Using the AWS CLI<a name="batch-ops-example-cli-job-tags-put-job-tagging"></a>

The following is an example of using `s3control put-job-tagging` to add job tags to your S3 Batch Operations job using the AWS CLI\.

**Note**  
If you send this request with an empty tag set, S3 Batch Operations deletes the existing tag set on the object\. Also, if you use this method, you are charged for a Tier 1 Request \(PUT\)\. For more information, see [Amazon S3 pricing](https://aws.amazon.com/s3/pricing)\.  
To delete existing tags for your Batch Operations job, the `DeleteJobTagging` action is preferred because it achieves the same result without incurring charges\.

1. Identify the job `TAGS` that you want for the job\. In this case, you apply two tags, `department` and `FiscalYear`, with the values `Marketing` and `2020` respectively\.

   ```
   read -d '' TAGS <<EOF
   [
     {
       "Key": "department",
       "Value": "Marketing"
     },
     {
       "Key": "FiscalYear",
       "Value": "2020"
     }
   ]
   EOF
   ```

1. Run the `put-job-tagging` action with the required parameters\.

   ```
   aws \
       s3control put-job-tagging \
       --account-id 123456789012 \
       --tags "${TAGS//$'\n'/}" \
       --job-id Example-e25a-4ed2-8bee-7f8ed7fc2f1c \
       --region us-east-1;
   ```

## Using the AWS SDK Java<a name="batch-ops-examples-java-job-with-tags-put"></a>

**Example**  
The following example puts the tags of an S3 Batch Operations job using the AWS SDK for Java\.  

```
public void putJobTagging(final AWSS3ControlClient awss3ControlClient,
                          final String jobId) {
    final S3Tag departmentTag = new S3Tag().withKey("department").withValue("Marketing");
    final S3Tag fiscalYearTag = new S3Tag().withKey("FiscalYear").withValue("2020");

    final PutJobTaggingRequest putJobTaggingRequest = new PutJobTaggingRequest()
            .withJobId(jobId)
            .withTags(departmentTag, fiscalYearTag);

    final PutJobTaggingResult putJobTaggingResult = awss3ControlClient.putJobTagging(putJobTaggingRequest);
}
```