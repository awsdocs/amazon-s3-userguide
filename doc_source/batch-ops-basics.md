--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# S3 Batch Operations basics<a name="batch-ops-basics"></a>

You can use S3 Batch Operations to perform large\-scale batch operations on Amazon S3 objects\. S3 Batch Operations can run a single operation or action on lists of Amazon S3 objects that you specify\. 

**Topics**
+ [Terminology](#batch-ops-terminology)
+ [How an S3 Batch Operations job works](#batch-ops-basics-how-it-works)

## Terminology<a name="batch-ops-terminology"></a>

This section uses the terms *jobs*, *operations*, and *tasks*, which are defined as follows:

**Job**  
A job is the basic unit of work for S3 Batch Operations\. A job contains all of the information necessary to run the specified operation on the objects listed in the manifest\. After you provide this information and request that the job begin, the job executes the operation for each object in the manifest\. 

**Operation**  
The operation is the type of API [action](https://docs.aws.amazon.com/AmazonS3/latest/API/API_Operations.html), such as copying objects, that you want the Batch Operations job to run\. Each job performs a single type of operation across all objects that are specified in the manifest\.

**Task**  
A task is the unit of execution for a job\. A task represents a single call to an Amazon S3 or AWS Lambda API operation to perform the job's operation on a single object\. Over the course of a job's lifetime, S3 Batch Operations create one task for each object specified in the manifest\.

## How an S3 Batch Operations job works<a name="batch-ops-basics-how-it-works"></a>

A job is the basic unit of work for S3 Batch Operations\. A job contains all of the information necessary to run the specified operation on a list of objects\. To create a job, you give S3 Batch Operations a list of objects and specify the action to perform on those objects\. 

S3 Batch Operations supports the following operations:
+ [Copy](batch-ops-copy-object.md)
+ [Restore](batch-ops-initiate-restore-object.md)
+ [Invoke AWS Lambda function](batch-ops-invoke-lambda.md)
+ [Replace access control list](batch-ops-put-object-acl.md)
+ [Replace all object tags](batch-ops-put-object-tagging.md)
+ [Delete all object tags](batch-ops-delete-object-tagging.md)
+ [Object Lock retention](batch-ops-retention-date.md)
+ [Object Lock legal hold](batch-ops-legal-hold.md)

A batch job performs a specified operation on every object that is included in its *manifest*\. A manifest lists the objects that you want a batch job to process and it is stored as an object in a bucket\. You can use a CSV\-formatted [ Amazon S3 inventory](storage-inventory.md) report as a manifest, which makes it easy to create large lists of objects located in a bucket\. You can also specify a manifest in a simple CSV format that enables you to perform batch operations on a customized list of objects contained within a single bucket\. 

After you create a job, Amazon S3 processes the list of objects in the manifest and runs the specified operation against each object\. While a job is executing, you can monitor its progress programmatically or through the Amazon S3 console\. You can also configure a job to generate a completion report when it finishes\. The completion report describes the results of each task that was performed by the job\. For more information about monitoring jobs, see [Managing S3 Batch Operations jobs](batch-ops-managing-jobs.md)\.