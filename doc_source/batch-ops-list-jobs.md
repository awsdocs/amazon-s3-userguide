# Listing jobs<a name="batch-ops-list-jobs"></a>

You can retrieve a list of your S3 Batch Operations jobs\. The list includes jobs that haven't yet finished and jobs that finished within the last 90 days\. The job list includes information for each job, such as its ID, description, priority, current status, and the number of tasks that have succeeded and failed\. You can filter your job list by status\. When you retrieve a job list through the console, you can also search your jobs by description or ID and filter them by AWS Region\.

## Get a list of Active and Complete jobs<a name="batch-ops-example-cli-active-jobs"></a>

The following AWS CLI example gets a list of `Active` and `Complete` jobs\. 

```
aws s3control list-jobs \
    --region us-west-2 \
    --account-id acct-id \
    --job-statuses '["Active","Complete"]' \
    --max-results 20
```

