# Managing replication rules using the Amazon S3 console<a name="disable-replication"></a>

Replication is the automatic, asynchronous copying of objects across buckets in the same or different AWS Regions\. It replicates newly created objects and object updates from a source bucket to a specified destination bucket\. 

You use the Amazon S3 console to add replication rules to the source bucket\. Replication rules define the source bucket objects to replicate and the destination bucket or buckets where the replicated objects are stored\. For more information about replication, see [Replicating objects](replication.md)\.

You can manage replication rules on the **Replication** page\. You can add, view, enable, disable, delete, and change the priority of the replication rules\. For information about adding replication rules to a bucket, see [Using the S3 console](enable-replication.md)\.

**To manage the replication rules for an S3 bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want\.

1. Choose **Management**, and then scroll down to **Replication rules**\.

1. You change the replication rules in the following ways\.
   + To enable or disable a replication rule, select the rule, choose **Actions**, and in the drop\-down list, choose **Enable rule** or **Disable rule**\. You can also disable, enable, or delete all the rules in the bucket from the **Actions** drop\-down list\.
   + To change the rule priorities, select the rule and choose **Edit**, which starts the Replication wizard to help you make the change\. For information about using the wizard, see [Using the S3 console](enable-replication.md)\.

     You set rule priorities to avoid conflicts caused by objects that are included in the scope of more than one rule\. In the case of overlapping rules, Amazon S3 uses the rule priority to determine which rule to apply\. The higher the number, the higher the priority\. For more information about rule priority, see [Replication configuration](replication-add-config.md)\.