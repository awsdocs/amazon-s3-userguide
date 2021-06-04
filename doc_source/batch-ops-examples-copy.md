# Copying objects using S3 Batch Operations<a name="batch-ops-examples-copy"></a>

You can use S3 Batch Operations to create a PUT copy job to copy objects within the same account or to a different destination account\. The following sections contain examples of how to store and use a manifest that is in a different account\. In the first section, you can use Amazon S3 Inventory to deliver the inventory report to the destination account for use during job creation or, you can use a comma\-separated values \(CSV\) manifest in the source or destination account, as shown in the second example\. The third example shows how to use the Copy operation to activate S3 Bucket Key encryption on existing objects\.

**Topics**
+ [Using an inventory report delivered to the destination account to copy objects across AWS accounts](specify-batchjob-manifest-xaccount-inventory.md)
+ [Using a CSV manifest stored in the source account to copy objects across AWS accounts](specify-batchjob-manifest-xaccount-csv.md)
+ [Using S3 Batch Operations to encrypt objects with S3 Bucket Keys](batch-ops-copy-example-bucket-key.md)

For examples that show the copy operation with tags using the AWS CLI and AWS SDK for Java, see [Creating a Batch Operations job with job tags used for labeling](batch-ops-tags-create.md)\.