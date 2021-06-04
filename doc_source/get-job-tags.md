# Getting the tags of a S3 Batch Operations job<a name="get-job-tags"></a>

You can use `GetJobTagging` to return the tags of an S3 Batch Operations job\. For more information, see the following examples\.

## Using the AWS CLI<a name="batch-ops-example-cli-job-tags-get-job-tagging"></a>

The following example gets the tags of a Batch Operations job using the AWS CLI\.

```
aws \
    s3control get-job-tagging \
    --account-id 123456789012 \
    --job-id Example-e25a-4ed2-8bee-7f8ed7fc2f1c \
    --region us-east-1;
```

## Using the AWS SDK Java<a name="batch-ops-examples-java-job-with-tags-get"></a>

**Example**  
The following example gets the tags of an S3 Batch Operations job using the AWS SDK for Java\.  

```
public List<S3Tag> getJobTagging(final AWSS3ControlClient awss3ControlClient,
                                 final String jobId) {
    final GetJobTaggingRequest getJobTaggingRequest = new GetJobTaggingRequest()
            .withJobId(jobId);

    final GetJobTaggingResult getJobTaggingResult =
            awss3ControlClient.getJobTagging(getJobTaggingRequest);

    final List<S3Tag> tags = getJobTaggingResult.getTags();

    return tags;
}
```