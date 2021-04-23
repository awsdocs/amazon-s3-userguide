# Performing S3 Batch Operations<a name="batch-ops"></a>

You can use S3 Batch Operations to perform large\-scale batch operations on Amazon S3 objects\. S3 Batch Operations can perform a single operation on lists of Amazon S3 objects that you specify\. A single job can perform a specified operation on billions of objects containing exabytes of data\. Amazon S3 tracks progress, sends notifications, and stores a detailed completion report of all actions, providing a fully managed, auditable, and serverless experience\. You can use S3 Batch Operations through the AWS console, AWS CLI, AWS SDKs, or REST API\.

Use S3 Batch Operations to copy objects and set object tags or access control lists \(ACLs\)\. You can also initiate object restores from Amazon S3 Glacier or invoke an AWS Lambda function to perform custom actions using your objects\. You can perform these operations on a custom list of objects, or you can use an Amazon S3 inventory report to easily generate lists of objects\. Amazon S3 Batch Operations use the same Amazon S3 APIs that you already use with Amazon S3, so you'll find the interface familiar\. 

**Topics**
+ [S3 Batch Operations basics](batch-ops-basics.md)
+ [Granting permissions for Amazon S3 Batch Operations](batch-ops-iam-role-policies.md)
+ [Creating an S3 Batch Operations job](batch-ops-create-job.md)
+ [Operations](batch-ops-operations.md)
+ [Managing S3 Batch Operations jobs](batch-ops-managing-jobs.md)
+ [Controlling access and labeling jobs using tags](batch-ops-job-tags.md)
+ [Managing S3 Object Lock using S3 Batch Operations](managing-object-lock-batchops.md)
+ [Copying objects across AWS accounts using S3 Batch Operations](batch-ops-examples-xcopy.md)
+ [Tracking an S3 Batch Operations job in Amazon EventBridge through AWS CloudTrail](batch-ops-examples-event-bridge-cloud-trail.md)
+ [S3 Batch Operations completion reports](batch-ops-examples-reports.md)

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/hUv34voEftc//0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/hUv34voEftc/)