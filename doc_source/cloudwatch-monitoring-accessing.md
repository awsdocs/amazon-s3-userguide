# Accessing CloudWatch metrics<a name="cloudwatch-monitoring-accessing"></a>

You can use the following procedures to view the storage metrics for Amazon S3\. To get the Amazon S3 metrics involved, you must set a start and end timestamp\. For metrics for any given 24\-hour period, set the time period to 86400 seconds, the number of seconds in a day\. Also, remember to set the `BucketName` and `StorageType` dimensions\.

## Using the AWS CLI<a name="accessing-cw-metrics-cli"></a>

For example, if you use the AWS CLI to get the average of a specific bucket's size in bytes, you could use the following command\.

```
aws cloudwatch get-metric-statistics --metric-name BucketSizeBytes --namespace AWS/S3 --start-time 2016-10-19T00:00:00Z --end-time 2016-10-20T00:00:00Z --statistics Average --unit Bytes --region us-west-2 --dimensions Name=BucketName,Value=ExampleBucket Name=StorageType,Value=StandardStorage --period 86400 --output json
```

This example produces the following output\.

```
{
    "Datapoints": [
        {
            "Timestamp": "2016-10-19T00:00:00Z", 
            "Average": 1025328.0, 
            "Unit": "Bytes"
        }
    ], 
    "Label": "BucketSizeBytes"
}
```

## Using the S3 console<a name="accessing-cw-metrics-console"></a>

**To view metrics by using the Amazon CloudWatch console**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the left navigation pane, choose **Metrics**\. 

1. Choose the **S3** namespace\.

1. \(Optional\) To view a metric, enter the metric name in the search box\.

1. \(Optional\) To filter by the **StorageType** dimension, enter the name of the storage class in the search box\.

**To view a list of valid metrics stored for your AWS account by using the AWS CLI**
+ At a command prompt, use the following command\.

  ```
  1. aws cloudwatch list-metrics --namespace "AWS/S3"
  ```

For more information about the permissions required to access CloudWatch dashboards, see [Amazon CloudWatch dashboard permissions](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/dashboard-permissions-update.html) in the *Amazon CloudWatch User Guide*\.