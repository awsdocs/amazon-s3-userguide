# Creating a metrics configuration that filters by object key name prefix or tag<a name="metrics-configurations-filter"></a>

There are three types of Amazon CloudWatch metrics for Amazon S3: storage metrics, request metrics, and replication metrics\. Storage metrics are reported once per day and are provided to all customers at no additional cost\. Request metrics are available at one\-minute intervals after some latency to process\. Request metrics are billed at the standard CloudWatch rate\. You must opt into request metrics by configuring them in the console or using the Amazon S3 API\.

For more information about CloudWatch metrics for Amazon S3, see [Monitoring metrics with Amazon CloudWatch](cloudwatch-monitoring.md)\. 

When you configure CloudWatch metrics, you can create a filter for all the objects in your bucket or you can filter the configuration into groups of related objects within a single bucket\. You can filter objects in a bucket for inclusion in a metrics configuration based on one or more of the following elements:
+ **Object key name prefix** – Although the Amazon S3 data model is a flat structure, you can infer hierarchy by using a prefix\. The Amazon S3 console supports these prefixes with the concept of folders\. If you filter by prefix, objects that have the same prefix are included in the metrics configuration\.
+ **Tag** – You can add tags, which are key\-value name pairs, to objects\. Tags help you find and organize objects easily\. You can also use tags as a filter for metrics configurations\.

If you specify a filter, only requests that operate on single objects can match the filter and be included in the reported metrics\. Requests like [Delete Multiple Objects](https://docs.aws.amazon.com/AmazonS3/latest/API/multiobjectdeleteapi.html) and `List` requests don't return any metrics for configurations with filters\.

To request more complex filtering, choose two or more elements\. Only objects that have all of those elements are included in the metrics configuration\. If you don't set filters, all of the objects in the bucket are included in the metrics configuration\.

## Using the S3 console<a name="configure-metrics-filter"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that contains the objects you want request metrics for\.

1. Choose the **Metrics** tab\.

1. Under **Bucket metrics**, choose **View additional charts**\.

1. Choose the **Request metrics** tab\.

1. Choose **Create filter**\.

1. In the **Filter name** box, enter your filter name\. 

   Names can only contain letters, numbers, periods, dashes, and underscores\.

1. Under **Choose a filter scope**, choose **Limit the scope of this filter using prefix and tags**\.

1. \(Optional\) In the **Prefix** box, enter a prefix to limit the scope of the filter to a single path\.

1. \(Optional\) Under **Tags**, enter a tag **Key** and **Value**\.

1. Choose **Create filter**\.

   Amazon S3 creates a filter that uses the tags or prefixes you specified\.

1. On the **Request metrics** tab, under **Filters**, choose the filter that you just created\.

   You have now created a filter that limits the request metrics scope by object tags and prefixes\. About 15 minutes after CloudWatch begins tracking these request metrics, you can see charts for the metrics on both the Amazon S3 and CloudWatch consoles\. Request metrics are billed at the standard CloudWatch rate\. For more information, see [Amazon CloudWatch pricing](http://aws.amazon.com/cloudwatch/pricing/)\. 

   You can also configure request metrics at the bucket level\. For information, see [Creating a CloudWatch metrics configuration for all the objects in your bucket](configure-request-metrics-bucket.md)\.

## Using the REST API<a name="configure-cw-filter-rest"></a>

You can also add metrics configurations programmatically with the Amazon S3 REST API\. For more information about adding and working with metrics configurations, see the following topics in the *Amazon Simple Storage Service API Reference*:
+ [PUT Bucket Metric Configuration](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTMetricConfiguration.html)
+ [GET Bucket Metric Configuration](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETMetricConfiguration.html)
+ [List Bucket Metric Configuration](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTListBucketMetricsConfiguration.html)
+ [DELETE Bucket Metric Configuration](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTDeleteBucketMetricsConfiguration.html)