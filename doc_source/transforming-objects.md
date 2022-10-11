# Transforming objects with S3 Object Lambda<a name="transforming-objects"></a>

With Amazon S3 Object Lambda, you can add your own code to Amazon S3 `GET`, `LIST`, and `HEAD` requests to modify and process data as it is returned to an application\. You can use custom code to modify the data returned by S3 `GET` requests to filter rows, dynamically resize images, redact confidential data, and more\. You can also use S3 Object Lambda to modify the output of S3 `LIST` requests to create a custom view of all objects in a bucket and S3 `HEAD` requests to modify object metadata such as object name and size\. Powered by AWS Lambda functions, your code runs on infrastructure that is fully managed by AWS\. Using S3 Object Lambda reduces the need to create and store derivative copies of your data or to run proxies, all with no need to change your applications\.

**How S3 Object Lambda works**  
S3 Object Lambda uses AWS Lambda functions to automatically process the output of standard S3 `GET`, `LIST`, or `HEAD` requests\. AWS Lambda is a serverless compute service that runs customer\-defined code without requiring management of underlying compute resources\. You can author and run your own custom Lambda functions, tailoring the data transformation to your specific use cases\. 

After you configure a Lambda function, you attach it to an S3 Object Lambda service endpoint, known as an *Object Lambda access point*\. The Object Lambda access point uses a standard S3 access point, known as a *supporting access point*, to access Amazon S3\. 

When you send a request to your Object Lambda access point, Amazon S3 automatically calls your Lambda function\. Any data retrieved by using an S3 `GET`, `LIST`, or `HEAD` request through the Object Lambda access point returns a transformed result back to the application\. All other requests are processed as normal, as illustrated in the following diagram\. 



![\[S3 Object Lambda diagram, showing how GET, LIST, or HEAD requests from a client application are transformed when they're made through Object Lambda access points.\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/ObjectLamdaDiagram.png)

The topics in this section describe how to work with S3 Object Lambda\.

**Topics**
+ [Creating Object Lambda access points](olap-create.md)
+ [Using Amazon S3 Object Lambda Access Points](olap-use.md)
+ [Security considerations for S3 Object Lambda access points](olap-security.md)
+ [Writing Lambda functions for S3 Object Lambda access points](olap-writing-lambda.md)
+ [Using AWS built Lambda functions](olap-examples.md)
+ [Best practices and guidelines for S3 Object Lambda](olap-best-practices.md)
+ [S3 Object Lambda tutorials](olap-tutorials.md)
+ [Debugging S3 Object Lambda](olap-debugging-lambda.md)

For S3 Object Lambda tutorials, see [Tutorial: Transforming data for your application with S3 Object Lambda](tutorial-s3-object-lambda-uppercase.md) and [Tutorial: Detecting and redacting PII data with S3 Object Lambda and Amazon Comprehend](tutorial-s3-object-lambda-redact-pii.md)\.

For more information about standard access points, see [Managing data access with Amazon S3 access points](access-points.md)\. 

For information about working with buckets, see [Buckets overview](UsingBucket.md)\. For information about working with objects, see [Amazon S3 objects overview](UsingObjects.md)\.