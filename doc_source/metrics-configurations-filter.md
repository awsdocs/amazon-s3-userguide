# Creating a metrics configuration that filters by prefix, object tag, or access point<a name="metrics-configurations-filter"></a>

There are three types of Amazon CloudWatch metrics for Amazon S3: storage metrics, request metrics, and replication metrics\. Storage metrics are reported once per day and are provided to all customers at no additional cost\. Request metrics are available at one\-minute intervals after some latency for processing\. Request metrics are billed at the standard CloudWatch rate\. You must opt in to request metrics by configuring them in the console or using the Amazon S3 API\.

For more information about CloudWatch metrics for Amazon S3, see [Monitoring metrics with Amazon CloudWatch](cloudwatch-monitoring.md)\. 

When you configure CloudWatch metrics, you can create a filter for all the objects in your bucket, or you can filter the configuration into groups of related objects within a single bucket\. You can filter objects in a bucket for inclusion in a metrics configuration based on one or more of the following filter types:
+ **Object key name prefix** – Although the Amazon S3 data model is a flat structure, you can infer a hierarchy by using a prefix\. The Amazon S3 console supports these prefixes with the concept of folders\. If you filter by prefix, objects that have the same prefix are included in the metrics configuration\. For more information about prefixes, see [Organizing objects using prefixes](using-prefixes.md)\. 
+ **Tag** – You can add tags, which are key\-value name pairs, to objects\. Tags help you find and organize objects easily\. You can also use tags as filters for metrics configurations\. For more information about object tags, see [Categorizing your storage using tags](object-tagging.md)\. 
+ **Access point** – S3 Access Points are named network endpoints that are attached to buckets and simplify managing data access at scale for shared datasets in S3\. When you create an access point filter, Amazon S3 includes requests to the access point that you specify in the metrics configuration\. For more information, see [Monitoring and logging access points](access-points-monitoring-logging.md)\.
**Note**  
When you create a metrics configuration that filters by access point, you must use the access point Amazon Resource Name \(ARN\), not the access point alias\. Make sure that you use the ARN for the access point itself, not the ARN for a specific object\. For more information about access point ARNs, see [Using access points](using-access-points.md)\.

If you specify a filter, only requests that operate on single objects can match the filter and be included in the reported metrics\. Requests like [Delete Multiple Objects](https://docs.aws.amazon.com/AmazonS3/latest/API/multiobjectdeleteapi.html)and `List` requests don't return any metrics for configurations with filters\.

To request more complex filtering, choose two or more elements\. Only objects that have all of those elements are included in the metrics configuration\. If you don't set filters, all of the objects in the bucket are included in the metrics configuration\.

## Using the S3 console<a name="configure-metrics-filter"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that contains the objects that you want request metrics for\.

1. Choose the **Metrics** tab\.

1. Under **Bucket metrics**, choose **View additional charts**\.

1. Choose the **Request metrics** tab\.

1. Choose **Create filter**\.

1. In the **Filter name** box, enter your filter name\. 

   Names can contain only letters, numbers, periods, dashes, and underscores\.

1. Under **Filter scope**, choose **Limit the scope of this filter using a prefix, object tags, and an S3 Access Point, or a combination of all three**\.

1. Under **Filter type**, choose at least one filter type: **Prefix**, **Object tags**, or **Access Point**\.

1. To define a prefix filter and limit the scope of the filter to a single path, in the **Prefix** box, enter a prefix\.

1. To define an object tags filter, under **Object tags**, choose **Add tag**, and then enter a tag **Key** and **Value**\.

1. To define an access point filter, in the **S3 Access Point** field, enter the access point ARN, or choose **Browse S3** to navigate to the access point\.
**Important**  
You cannot enter an access point alias\. You must enter the ARN for the access point itself, not the ARN for a specific object\.

1. Choose **Save changes**\.

   Amazon S3 creates a filter that uses the prefix, tags, or access point that you specified\.

1. On the **Request metrics** tab, under **Filters**, choose the filter that you just created\.

   You have now created a filter that limits the request metrics scope by prefix, object tags, or access point\. About 15 minutes after CloudWatch begins tracking these request metrics, you can see charts for the metrics on both the Amazon S3 and CloudWatch consoles\. Request metrics are billed at the standard CloudWatch rate\. For more information, see [Amazon CloudWatch pricing](http://aws.amazon.com/cloudwatch/pricing/)\. 

   You can also configure request metrics at the bucket level\. For information, see [Creating a CloudWatch metrics configuration for all the objects in your bucket](configure-request-metrics-bucket.md)\.

## Using the AWS CLI<a name="add-metrics-configurations"></a>

1. Install and set up the AWS CLI\. For instructions, see [Installing, updating, and uninstalling the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) in the *AWS Command Line Interface User Guide*\.

1. Open a terminal\.

1. To add a metrics configuration, run one of the following commands:  
**Example : To filter by prefix**  

   ```
   aws s3api put-bucket-metrics-configuration --bucket DOC-EXAMPLE-BUCKET1 --id metrics-config-id --metrics-configuration '{"Id":"metrics-config-id", "Filter":{"Prefix":"prefix1"}} '
   ```  
**Example : To filter by tags**  

   ```
   aws s3api put-bucket-metrics-configuration --bucket DOC-EXAMPLE-BUCKET1 --id metrics-config-id --metrics-configuration '{"Id":"metrics-config-id", "Filter":{"Tag": {"Key": "string", "Value": "string"}} '
   ```  
**Example : To filter by access point**  

   ```
   aws s3api put-bucket-metrics-configuration --bucket DOC-EXAMPLE-BUCKET1 --id metrics-config-id --metrics-configuration '{"Id":"metrics-config-id", "Filter":{"AccessPointArn":"arn:aws:s3:Region:account-id:accesspoint/access-point-name"}} '
   ```  
**Example : To filter by prefix, tags, and access point**  

   ```
   aws s3api put-bucket-metrics-configuration --endpoint https://s3.Region.amazonaws.com --bucket DOC-EXAMPLE-BUCKET1 --id metrics-config-id --metrics-configuration '
   {
       "Id": "metrics-config-id",
       "Filter": {
           "And": {
               "Prefix": "string",
               "Tags": [
                   {
                       "Key": "string",
                       "Value": "string"
                   }
               ],
               "AccessPointArn": "arn:aws:s3:Region:account-id:accesspoint/access-point-name"
           }
       }
   }'
   ```

## Using the REST API<a name="configure-cw-filter-rest"></a>

You can also add metrics configurations programmatically with the Amazon S3 REST API\. For more information about adding and working with metrics configurations, see the following topics in the *Amazon Simple Storage Service API Reference*:
+ [PUT Bucket Metric Configuration](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTMetricConfiguration.html)
+ [GET Bucket Metric Configuration](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETMetricConfiguration.html)
+ [List Bucket Metric Configuration](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTListBucketMetricsConfiguration.html)
+ [DELETE Bucket Metric Configuration](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTDeleteBucketMetricsConfiguration.html)