# Transforming objects with S3 Object Lambda<a name="transforming-objects"></a>

With S3 Object Lambda you can add your own code to Amazon S3 GET requests to modify and process data as it is returned to an application\. You can use custom code to modify the data returned by standard S3 GET requests to filter rows, dynamically resize images, redact confidential data, and more\. Powered by AWS Lambda functions, your code runs on infrastructure that is fully managed by AWS, eliminating the need to create and store derivative copies of your data or to run proxies, all with no changes required to applications\.

S3 Object Lambda uses AWS Lambda functions to automatically process the output of a standard S3 GET request\. AWS Lambda is a serverless compute service that runs customer\-defined code without requiring management of underlying compute resources\. You can author and execute your own custom Lambda functions, tailoring data transformation to your specific use cases\. You can configure a Lambda function and attach it to an S3 Object Lambda service endpoint and S3 will automatically call your function\. Then any data retrieved using an S3 GET request through the S3 Object Lambda endpoint will return a transformed result back to the application\. All other requests will be processed as normal, as illustrated in the following diagram\. 

![\[S3 Object Lambda diagram.\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/ObjectLamdaDiagram.png)

The topics in this section describe how to work with Object Lambda access points\.

**Topics**
+ [Creating Object Lambda Access Points](olap-create.md)
+ [Configuring IAM policies for Object Lambda access points](olap-policies.md)
+ [Writing and debugging Lambda functions for S3 Object Lambda Access Points](olap-writing-lambda.md)
+ [Using AWS built Lambda functions](olap-examples.md)
+ [Best practices and guidelines for S3 Object Lambda](olap-best-practices.md)
+ [Security considerations for S3 Object Lambda access points](olap-security.md)

For S3 Object Lambda tutorials, see [Tutorial: Transforming data for your application with S3 Object Lambda](tutorial-s3-object-lambda-uppercase.md) and [Tutorial: Detecting and redacting PII data with S3 Object Lambda and Amazon Comprehend](tutorial-s3-object-lambda-redact-pii.md)\.

For more information about standard access points, see [Managing data access with Amazon S3 access points](access-points.md)\. 

For information about working with buckets, see [Buckets overview](UsingBucket.md)\. For information about working with objects, see [Amazon S3 objects overview](UsingObjects.md)\.