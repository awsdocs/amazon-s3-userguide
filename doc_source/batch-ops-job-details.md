--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Viewing job details<a name="batch-ops-job-details"></a>

If you want more information about a job than you can retrieve by listing jobs, you can view all of the details for a single job\. In addition to the information returned in a job list, a single job's details include other items\. This information includes the operation parameters, details about the manifest, information about the completion report \(if you configured one when you created the job\), and the Amazon Resource Name \(ARN\) of the user role that you assigned to run the job\. By viewing an individual job's details, you can access a job's entire configuration\. 

## Get the description of an S3 Batch Operations job<a name="batch-ops-example-cli-job-description"></a>

The following example gets the description of an S3 Batch Operations job using the AWS CLI\. 

```
aws s3control describe-job \
    --region us-west-2 \
    --account-id acct-id \
    --job-id 00e123a4-c0d8-41f4-a0eb-b46f9ba5b07c
```

