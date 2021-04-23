# Deleting the tags from an S3 Batch Operations job<a name="delete-job-tags"></a>

You can use these examples to delete the tags from a Batch Operations job\.

## Using the AWS CLI<a name="batch-ops-example-cli-job-tags-delete-job-tagging"></a>

The following example deletes the tags from a Batch Operations job using the AWS CLI\.

```
aws \
    s3control delete-job-tagging \
    --account-id 123456789012 \
    --job-id Example-e25a-4ed2-8bee-7f8ed7fc2f1c \
    --region us-east-1;
```

## Delete the job tags of a Batch Operations job<a name="batch-ops-examples-java-job-with-tags-delete"></a>

**Example**  
The following example deletes the tags of an S3 Batch Operations job using the AWS SDK for Java\.  

```
public void deleteJobTagging(final AWSS3ControlClient awss3ControlClient,
                             final String jobId) {
    final DeleteJobTaggingRequest deleteJobTaggingRequest = new DeleteJobTaggingRequest()
            .withJobId(jobId);

    final DeleteJobTaggingResult deleteJobTaggingResult =
                awss3ControlClient.deleteJobTagging(deleteJobTaggingRequest);
}
```