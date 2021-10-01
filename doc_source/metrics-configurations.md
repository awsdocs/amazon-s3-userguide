# CloudWatch metrics configurations<a name="metrics-configurations"></a>

With Amazon CloudWatch request metrics for Amazon S3, you can receive 1\-minute CloudWatch metrics, set CloudWatch alarms, and access CloudWatch dashboards to view near\-real\-time operations and performance of your Amazon S3 storage\. For applications that depend on cloud storage, these metrics let you quickly identify and act on operational issues\. When enabled, these 1\-minute metrics are available at the Amazon S3 bucket\-level, by default\.

If you want to get the CloudWatch request metrics for the objects in a bucket, you must create a metrics configuration for the bucket\. For more information, see [Creating a CloudWatch metrics configuration for all the objects in your bucket](configure-request-metrics-bucket.md)\. 

You can also use a shared prefix, object tags, or an access point to define a filter for the metrics collected\. This method of defining a filter allows you to align metrics filters to specific business applications, workflows, or internal organizations\. For more information, see [Creating a metrics configuration that filters by prefix, object tag, or access point](metrics-configurations-filter.md)\. For more information about the CloudWatch metrics that are available and the differences between storage and request metrics, see [Monitoring metrics with Amazon CloudWatch](cloudwatch-monitoring.md)\.

Keep the following in mind when using metrics configurations:
+ You can have a maximum of 1,000 metrics configurations per bucket\.
+ You can choose which objects in a bucket to include in metrics configurations by using filters\. You can filter on a shared prefix, object tag, or access point to align metrics filters to specific business applications, workflows, or internal organizations\. To request metrics for the entire bucket, create a metrics configuration without a filter\.
+ Metrics configurations are necessary only to enable request metrics\. Bucket\-level daily storage metrics are always turned on, and are provided at no additional cost\. Currently, it's not possible to get daily storage metrics for a filtered subset of objects\.
+ Each metrics configuration enables the full set of [available request metrics](metrics-dimensions.md#s3-request-cloudwatch-metrics)\. Operation\-specific metrics \(such as `PostRequests`\) are reported only if there are requests of that type for your bucket or your filter\.
+ Request metrics are reported for object\-level operations\. They are also reported for operations that list bucket contents, like [GET Bucket \(List Objects\)](https://docs.aws.amazon.com/AmazonS3/latest/API/v2-RESTBucketGET.html), [GET Bucket Object Versions](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETVersion.html), and [List Multipart Uploads](https://docs.aws.amazon.com/AmazonS3/latest/API/mpUploadListMPUpload.html), but they are not reported for other operations on buckets\.
+ Request metrics support filtering by prefix, object tags, or access point, but storage metrics do not\.

## Best\-effort CloudWatch metrics delivery<a name="metrics-configurations-delivery"></a>

 CloudWatch metrics are delivered on a best\-effort basis\. Most requests for an Amazon S3 object that have request metrics result in a data point being sent to CloudWatch\.

The completeness and timeliness of metrics are not guaranteed\. The data point for a particular request might be returned with a timestamp that is later than when the request was actually processed\. The data point for a minute might be delayed before being available through CloudWatch, or it might not be delivered at all\. CloudWatch request metrics give you an idea of the nature of traffic against your bucket in near\-real time\. It is not meant to be a complete accounting of all requests\.

It follows from the best\-effort nature of this feature that the reports available at the [Billing & Cost Management Dashboard](https://console.aws.amazon.com/billing/home?#/) might include one or more access requests that do not appear in the bucket metrics\.

For more information about working with CloudWatch metrics in Amazon S3, see the following topics\.

**Topics**
+ [Best\-effort CloudWatch metrics delivery](#metrics-configurations-delivery)
+ [Creating a CloudWatch metrics configuration for all the objects in your bucket](configure-request-metrics-bucket.md)
+ [Creating a metrics configuration that filters by prefix, object tag, or access point](metrics-configurations-filter.md)
+ [Deleting a metrics filter](delete-request-metrics-filter.md)