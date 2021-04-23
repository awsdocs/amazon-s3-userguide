# Creating a CloudWatch metrics configuration for all the objects in your bucket<a name="configure-request-metrics-bucket"></a>

When you configure request metrics, you can create a CloudWatch metrics configuration for all the objects in your bucket, or you can filter by prefix or object tag\. 

The procedures in this topic show you how to create a configuration for all the objects in your bucket\. To create a configuration that filters by object tag or prefix, see [Creating a metrics configuration that filters by object key name prefix or tag](metrics-configurations-filter.md)\.

There are three types of Amazon CloudWatch metrics for Amazon S3: storage metrics, request metrics, and replication metrics\. Storage metrics are reported once per day and are provided to all customers at no additional cost\. Request metrics are available at one\-minute intervals after some latency to process\. Request metrics are billed at the standard CloudWatch rate\. You must opt into request metrics by configuring them in the console or using the Amazon S3 API\.

For more information about CloudWatch metrics for Amazon S3, see [Monitoring metrics with Amazon CloudWatch](cloudwatch-monitoring.md)\. 

You can add metrics configurations to a bucket using the Amazon S3 console, the AWS Command Line Interface \(AWS CLI\), or the Amazon S3 REST API\.

## Using the S3 console<a name="configure-metrics"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that contains the objects you want request metrics for\.

1. Choose the **Metrics** tab\.

1. Under **Bucket metrics**, choose **View additional charts**\.

1. Choose the **Request metrics** tab\.

1. Choose **Create filter**\.

1. In the **Filter name** box, enter your filter name\. 

   Names can only contain letters, numbers, periods, dashes, and underscores\. We recommend using the name `EntireBucket` for a filter that applies to all objects\.

1. Under **Choose a filter scope**, choose **This filter applies to all objects in the bucket**\.

   You can also define a filter so that the metrics are only collected and reported on a subset of objects in the bucket\. For more information, see [Creating a metrics configuration that filters by object key name prefix or tag](metrics-configurations-filter.md)\.

1. Choose **Create filter**\.

1. On the **Request metrics** tab, under **Filters**, choose the filter that you just created\.

   After about 15 minutes, CloudWatch begins tracking these request metrics\. You can see them on the **Request metrics** tab\. You can see graphs for the metrics on the Amazon S3 or CloudWatch console\. Request metrics are billed at the standard CloudWatch rate\. For more information, see [Amazon CloudWatch pricing](http://aws.amazon.com/cloudwatch/pricing/)\. 

## Using the AWS CLI<a name="add-metrics-configurations"></a>

1. Install and set up the AWS CLI\. For instructions, see [Installing, updating, and uninstalling the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-getting-set-up.html) in the *AWS Command Line Interface User Guide*\.

1. Open a terminal\.

1. Run the following command to add a metrics configuration\.

   ```
   aws s3api put-bucket-metrics-configuration --endpoint https://s3.us-west-2.amazonaws.com --bucket bucket-name --id metrics-config-id --metrics-configuration '{"Id":"metrics-config-id","Filter":{"Prefix":"prefix1"}}'
   ```

1. To verify that the configuration was added, run the following command\.

   ```
   aws s3api get-bucket-metrics-configuration --endpoint https://s3.us-west-2.amazonaws.com --bucket bucket-name --id metrics-config-id
   ```

   This returns the following response\.

   ```
   {
       "MetricsConfiguration": {
           "Filter": {
               "Prefix": "prefix1"
           },
           "Id": "metrics-config-id"
       }
   }
   ```

## Using the REST API<a name="metrics-configuration-api"></a>

You can also add metrics configurations programmatically with the Amazon S3 REST API\. For more information about adding and working with metrics configurations, see the following topics in the *Amazon Simple Storage Service API Reference*:
+ [PUT Bucket Metric Configuration](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTMetricConfiguration.html)
+ [GET Bucket Metric Configuration](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETMetricConfiguration.html)
+ [List Bucket Metric Configuration](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTListBucketMetricsConfiguration.html)
+ [DELETE Bucket Metric Configuration](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTDeleteBucketMetricsConfiguration.html)