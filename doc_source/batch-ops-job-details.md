# Viewing job details<a name="batch-ops-job-details"></a>

If you want more information about a job than you can retrieve by listing jobs, you can view all of the details for a single job\. In addition to the information returned in a job list, a single job's details include other items, such as: 
+ The operation parameters
+ Details about the manifest
+ Information about the completion report \(if you configured one when you created the job\)
+ The Amazon Resource Name \(ARN\) of the user role that you assigned to run the job

By viewing an individual job's details, you can access a job's entire configuration\. To view a job’s details, you can use the Amazon S3 console or the AWS Command Line Interface \(AWS CLI\)\.

## Get an S3 Batch Operations job description in the Amazon S3 console<a name="batch-ops-console-job-description"></a>

**To view a Batch Operations job description by using the console**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Batch Operations**\.

1. Choose the job ID of the specific job to view its details\.

## Get an S3 Batch Operations job description in the AWS CLI<a name="batch-ops-example-cli-job-description"></a>

The following example gets the description of an S3 Batch Operations job by using the AWS CLI\. To use the following example command, replace the *`user input placeholders`* with your own information\.

```
            aws s3control describe-job \
            --region us-west-2 \
            --account-id acct-id \
            --job-id 00e123a4-c0d8-41f4-a0eb-b46f9ba5b07c
```

