# Working with Amazon S3 Storage Lens using the console and API<a name="S3LensExamples"></a>

Amazon S3 Storage Lens is a cloud\-storage analytics feature that you can use to gain organization\-wide visibility into object\-storage usage and activity\. You can use S3 Storage Lens metrics to generate summary insights, such as finding out how much storage you have across your entire organization or which are the fastest\-growing buckets and prefixes\. You can also use S3 Storage Lens metrics to identify cost\-optimization opportunities, implement data\-protection and security best practices, and improve the performance of application workloads\. For example, you can identify buckets that don't have S3 Lifecycle rules to expire incomplete multipart uploads that are more than 7 days old\. You can also identify buckets that aren't following data\-protection best practices, such as using S3 Replication or S3 Versioning\. S3 Storage Lens also analyzes metrics to deliver contextual recommendations that you can use to optimize storage costs and apply best practices for protecting your data\. 

S3 Storage Lens aggregates your metrics and displays the information in the **Account snapshot** section on the Amazon S3 console **Buckets** page\. S3 Storage Lens also provides an interactive dashboard that you can use to visualize insights and trends, flag outliers, and receive recommendations for optimizing storage costs and applying data\-protection best practices\. Your dashboard has drill\-down options to generate insights at the organization, account, bucket, object, or prefix level\. You can also send a once\-daily metrics export in CSV or Parquet format to an S3 bucket\. 

The following video shows a brief demonstration of how to use S3 Storage Lens on the Amazon S3 console\.

[![AWS Videos](http://img.youtube.com/vi/https://www.youtube.com/embed/TNmZEvwFiOA/0.jpg)](http://www.youtube.com/watch?v=https://www.youtube.com/embed/TNmZEvwFiOA)

The following sections contain examples of creating, updating, and viewing S3 Storage Lens configurations and performing operations related to the feature\. If you are using S3 Storage Lens with AWS Organizations, these examples also cover those use cases\. In the examples, replace any variable values with those that are specific to you\.

**Topics**
+ [Using Amazon S3 Storage Lens on the console](storage_lens_console.md)
+ [Amazon S3 Storage Lens examples using the AWS CLI](S3LensCLIExamples.md)
+ [Amazon S3 Storage Lens examples using the SDK for Java](S3LensJavaExamples.md)