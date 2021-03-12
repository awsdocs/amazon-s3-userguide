--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Managing S3 Batch Operations jobs<a name="batch-ops-managing-jobs"></a>

Amazon S3 provides a robust set of tools to help you manage your Batch Operations jobs after you create them\. This section describes the operations you can use to manage your jobs\. You can perform all of the operations listed in this section using the AWS Management Console, AWS CLI, AWS SDKs, or REST APIs\.

## Tracking job failure<a name="batch-ops-job-status-failure"></a>

If an S3 Batch Operations job encounters a problem that prevents it from running successfully, such as not being able to read the specified manifest, the job fails\. When a job fails, it generates one or more failure codes or failure reasons\. S3 Batch Operations store the failure codes and reasons with the job so that you can view them by requesting the job's details\. If you requested a completion report for the job, the failure codes and reasons also appear there\.

To prevent jobs from running a large number of unsuccessful operations, Amazon S3 imposes a task\-failure threshold on every Batch Operations job\. When a job has run at least 1,000 tasks, Amazon S3 monitors the task failure rate\. At any point, if the failure rate \(the number of tasks that have failed as a proportion of the total number of tasks that have run\) exceeds 50 percent, the job fails\. If your job fails because it exceeded the task\-failure threshold, you can identify the cause of the failures\. For example, you might have accidentally included some objects in the manifest that don't exist in the specified bucket\. After fixing the errors, you can resubmit the job\.

**Note**  
S3 Batch Operations operate asynchronously and the tasks don't necessarily run in the order that the objects are listed in the manifest\. Therefore, you can't use the manifest ordering to determine which objects' tasks succeeded and which ones failed\. Instead, you can examine the job's completion report \(if you requested one\) or view your AWS CloudTrail event logs to help determine the source of the failures\.

## Notifications and logging<a name="batch-ops-notifications"></a>

In addition to requesting completion reports, you can also capture, review, and audit Batch Operations activity using AWS CloudTrail\. Because Batch Operations use existing Amazon S3 APIs to perform tasks, those tasks also emit the same events that they would if you called them directly\. Thus, you can track and record the progress of your job and all of its tasks using the same notification, logging, and auditing tools and processes that you already use with Amazon S3\. For more information, see [Tracking an S3 Batch Operations job in Amazon EventBridge through AWS CloudTrail](batch-ops-examples-event-bridge-cloud-trail.md)\.

**Note**  
Amazon S3 Batch Operations generates both management and data events in CloudTrail during job execution\. The volume of these events scale with the number of keys in each job’s manifest\. Refer to the [CloudTrail pricing](http://aws.amazon.com/cloudtrail/pricing/) page for details, which includes examples of how pricing changes depending on the number of CloudTrails you have configured in your account\. To learn how to configure and log events to fit your needs, see [Create your first trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-tutorial.html#tutorial-step2) in the *AWS CloudTrail User Guide*\.

For more information about Amazon S3 events, see [Amazon S3 Event Notifications](NotificationHowTo.md)\. 

## Completion reports<a name="batch-ops-completion-report"></a>

When you create a job, you can request a completion report\. As long as S3 Batch Operations successfully invoke at least one task, Amazon S3 generates a completion report after it finishes running tasks, fails, or is canceled\. You can configure the completion report to include all tasks or only failed tasks\. 

The completion report includes the job configuration and status and information for each task, including the object key and version, status, error codes, and descriptions of any errors\. Completion reports provide an easy way to view the results of your tasks in a consolidated format with no additional setup required\. For an example of a completion report, see [S3 Batch Operations completion reports](batch-ops-examples-reports.md)\. 

If you don't configure a completion report, you can still monitor and audit your job and its tasks using CloudTrail and Amazon CloudWatch For more information, see [Tracking an S3 Batch Operations job in Amazon EventBridge through AWS CloudTrail](batch-ops-examples-event-bridge-cloud-trail.md)\.

**Topics**
+ [Tracking job failure](#batch-ops-job-status-failure)
+ [Notifications and logging](#batch-ops-notifications)
+ [Completion reports](#batch-ops-completion-report)
+ [Managing S3 Batch Operations jobs using the S3 console](batch-ops-manage-jobs.md)
+ [Listing jobs](batch-ops-list-jobs.md)
+ [Viewing job details](batch-ops-job-details.md)
+ [Assigning job priority](batch-ops-job-priority.md)
+ [Tracking job status](batch-ops-job-status.md)