--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Deleting a metrics filter<a name="delete-request-metrics-filter"></a>

You can delete an Amazon CloudWatch request metrics filter if you no longer need it\. When you delete a filter, you are no longer charged for request metrics that use that *specific filter*\. However, you will continue to be charged for any other filter configurations that exist\. 

When you delete a filter, you can no longer use the filter for request metrics\. Deleting a filter cannot be undone\. 

For information about creating a request metrics filter, see the following topics:
+ [Creating a CloudWatch metrics configuration for all the objects in your bucket](configure-request-metrics-bucket.md)
+ [Creating a metrics configuration that filters by object key name prefix or tag](metrics-configurations-filter.md)

## Using the S3 console<a name="delete-request-metrics-filter-console"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose your bucket name\.

1. Choose the **Metrics** tab\.

1. Under **Bucket metrics**, choose **View additional charts**\.

1. Choose the **Request metrics** tab\.

1. Choose **Manage filters**\.

1. Choose your filter\.
**Important**  
Deleting a filter cannot be undone\.

1. Choose **Delete**\.

   Amazon S3 deletes your filter\.

## Using the REST API<a name="delete-request-metrics-filter-rest"></a>

You can also add metrics configurations programmatically with the Amazon S3 REST API\. For more information about adding and working with metrics configurations, see the following topics in the *Amazon Simple Storage Service API Reference*:
+ [PUT Bucket Metric Configuration](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTMetricConfiguration.html)
+ [GET Bucket Metric Configuration](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETMetricConfiguration.html)
+ [List Bucket Metric Configuration](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTListBucketMetricsConfiguration.html)
+ [DELETE Bucket Metric Configuration](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTDeleteBucketMetricsConfiguration.html)