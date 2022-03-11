# Performing large\-scale batch operations on Amazon S3 objects<a name="batch-ops"></a>

You can use S3 Batch Operations to perform large\-scale batch operations on Amazon S3 objects\. S3 Batch Operations can perform a single operation on lists of Amazon S3 objects that you specify\. A single job can perform a specified operation on billions of objects containing exabytes of data\. Amazon S3 tracks progress, sends notifications, and stores a detailed completion report of all actions, providing a fully managed, auditable, and serverless experience\. You can use S3 Batch Operations through the AWS Management Console, AWS CLI, Amazon SDKs, or REST API\.

Use S3 Batch Operations to copy objects and set object tags or access control lists \(ACLs\)\. You can also initiate object restores from S3 Glacier Flexible Retrieval or invoke an AWS Lambda function to perform custom actions using your objects\. You can perform these operations on a custom list of objects, or you can use an Amazon S3 Inventory report to easily generate lists of objects\. Amazon S3 Batch Operations use the same Amazon S3 APIs that you already use with Amazon S3, so you'll find the interface familiar\. 

## S3 Batch Operations basics<a name="batch-ops-basics"></a>

You can use S3 Batch Operations to perform large\-scale batch operations on Amazon S3 objects\. S3 Batch Operations can run a single operation or action on lists of Amazon S3 objects that you specify\. 

### Terminology<a name="batch-ops-terminology"></a>

This section uses the terms *jobs*, *operations*, and *tasks*, which are defined as follows:

**Job**  
A job is the basic unit of work for S3 Batch Operations\. A job contains all of the information necessary to run the specified operation on the objects listed in the manifest\. After you provide this information and request that the job begin, the job performs the operation for each object in the manifest\. 

**Operation**  
The operation is the type of API [action](https://docs.aws.amazon.com/AmazonS3/latest/API/API_Operations.html), such as copying objects, that you want the Batch Operations job to run\. Each job performs a single type of operation across all objects that are specified in the manifest\.

**Task**  
A task is the unit of execution for a job\. A task represents a single call to an Amazon S3 or AWS Lambda API operation to perform the job's operation on a single object\. Over the course of a job's lifetime, S3 Batch Operations create one task for each object specified in the manifest\.

### How an S3 Batch Operations job works<a name="batch-ops-basics-how-it-works"></a>

A job is the basic unit of work for S3 Batch Operations\. A job contains all of the information necessary to run the specified operation on a list of objects\. To create a job, you give S3 Batch Operations a list of objects and specify the action to perform on those objects\. 

For information about the operations that S3 Batch Operations supports, see [Operations supported by S3 Batch Operations](batch-ops-operations.md)\.

A batch job performs a specified operation on every object that is included in its *manifest*\. A manifest lists the objects that you want a batch job to process and it is stored as an object in a bucket\. You can use a comma\-separated values \(CSV\)\-formatted [Amazon S3 Inventory](storage-inventory.md) report as a manifest, which makes it easy to create large lists of objects located in a bucket\. You can also specify a manifest in a simple CSV format that enables you to perform batch operations on a customized list of objects contained within a single bucket\. 

After you create a job, Amazon S3 processes the list of objects in the manifest and runs the specified operation against each object\. While a job is running, you can monitor its progress programmatically or through the Amazon S3 console\. You can also configure a job to generate a completion report when it finishes\. The completion report describes the results of each task that was performed by the job\. For more information about monitoring jobs, see [Managing S3 Batch Operations jobs](batch-ops-managing-jobs.md)\.