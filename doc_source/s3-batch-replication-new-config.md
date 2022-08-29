# Create a Batch Replication job for a first replication rule or new destination<a name="s3-batch-replication-new-config"></a>

When you create the first rule in a new replication configuration or add a new destination to an existing configuration through the AWS Management Console, you can optionally create a Batch Replication job\.

To use Batch Replication for an existing configuration without adding a new destination see, [Create a Batch Replication job for existing replication rules](s3-batch-replication-existing-config.md)\.

## Using Batch Replication for a new replication rule or destination through the AWS Management Console<a name="batch-replication-new-config-console"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that contains the objects that you want to replicate\.

1. To create a new replication rule or edit an existing rule, choose **Management**, and scroll down to **Replication rules**:
   + To create a new replication rule, choose **Create replication rule**\.
**Note**  
For examples on how to set up a basic replication rule see, [Walkthroughs: Examples for configuring replication](replication-example-walkthroughs.md)\.
   + To edit an existing replication rule, select the rule, and then choose **Edit rule**\.

1. Create your new replication rule or edit the destination for your existing replication rule, and choose **Save**\.

   After you create the first rule in a new replication configuration or edit an existing configuration to add a new destination, a **Replicate existing objects?** dialog appears, giving you the option to create a Batch Replication job\.

1. To create a Batch Replication job, choose **Yes, replicate existing objects**\.

   To run your Batch Replication job at a later time, choose **No, do not replicate existing objects**\.

1. Create your S3 Batch Replication job\. The S3 Batch Replication job has several settings:   
**Job run option**  
If you want the S3 Batch Replication job to run immediately, you can choose **Job runs automatically when ready**\. If you want to run the job at a later time, choose **Job waits to be run when ready**\.  
If you choose **Job runs automatically when ready**, you will not be able to create and save a Batch Operations manifest\. To save the Batch Operations manifest, choose **Job waits to be run when ready**\.  
**Batch Operations manifest**  
The manifest is a list of all of the objects that you want to run the specified action on\. You can choose to save the Batch Operations manifest\. Similar to S3 Inventory files, the manifest will be saved as a CSV file and stored in a bucket\. To learn more about Batch Operations manifests, see [Specifying a manifest](batch-ops-create-job.md#specify-batchjob-manifest)\.  
**Completion report**  
S3 Batch Operations execute one task for each object specified in the manifest\. Completion reports provide an easy way to view the results of your tasks in a consolidated format with no additional setup required\. You can request a completion report for all tasks or only for failed tasks\. To learn more about completion reports, see [Completion reports](batch-ops-job-status.md#batch-ops-completion-report)\.  
**Permissions**  
One of the most common causes of replication failures is insufficient permissions in the provided AWS Identity and Access Management \(IAM\) role\. For information about creating this role see, [Configuring IAM policies for Batch Replication](s3-batch-replication-policies.md)\.

1. Choose **Create Batch Operations job**\.