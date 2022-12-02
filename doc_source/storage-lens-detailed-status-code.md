# Using S3 Storage Lens metrics to improve performance<a name="storage-lens-detailed-status-code"></a>

If you have [S3 Storage Lens advanced metrics](storage_lens_basics_metrics_recommendations.md#storage_lens_basics_metrics_selection) enabled, you can use detailed status\-code metrics to get counts for successful or failed requests\. You can use this information to troubleshoot access or performance issues\. Detailed status\-code metrics show counts for HTTP status codes, such as 403 Forbidden and 503 Service Unavailable\. You can examine overall trends for detailed status\-code metrics across S3 buckets, accounts, and organizations\. Then, you can drill down into bucket\-level metrics to identify workloads that are currently accessing these buckets and causing errors\. 

For example, you can look at the **403 Forbidden error count** metric to identify workloads that are accessing buckets without the correct permissions applied\. After you've identified these workloads, you can do a deep dive outside of S3 Storage Lens to troubleshoot your 403 Forbidden errors\.

This example shows you how to do a trend analysis for the 403 Forbidden error by using the **403 Forbidden error count** and the **% 403 Forbidden errors** metrics\. You can use these metrics to identify workloads that are accessing buckets without the correct permissions applied\. You can do a similar trend analysis for any of the other **Detailed status code metrics**\. For more information, see [Amazon S3 Storage Lens metrics glossary](storage_lens_metrics_glossary.md)\.

**Prerequisite**  
To see **Detailed status code metrics** in your S3 Storage Lens dashboard, you must enable S3 Storage Lens **Advanced metrics and recommendations**, and then select **Detailed status code metrics**\. For more information, see [Creating and updating Amazon S3 Storage Lens dashboards](storage_lens_console_creating_editing.md)\.

**Topics**
+ [Step 1: Do a trend analysis for an individual HTTP status code](#storage-lens-detailed-status-code-step1)
+ [Step 2: Analyze error counts by bucket](#storage-lens-detailed-status-code-step2)
+ [Step 3: Troubleshoot errors](#storage-lens-detailed-status-code-step3)

## Step 1: Do a trend analysis for an individual HTTP status code<a name="storage-lens-detailed-status-code-step1"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the name of the dashboard that you want to view\.

1. In the **Trends and distributions** section, for **Primary metric**, choose **403 Forbidden error count** from the **Detailed status codes** category\. For **Secondary metric**, choose **% 403 Forbidden errors**\.

1. Scroll down to the **Top N overview for *date*** section\. For **Metrics**, choose **403 Forbidden error count** or **% 403 Forbidden errors** from the **Detailed status codes** category\.

   The **Top N overview for *date*** section updates to display the top 403 Forbidden error counts by account, AWS Region, and bucket\. 

## Step 2: Analyze error counts by bucket<a name="storage-lens-detailed-status-code-step2"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the name of the dashboard that you want to view\.

1. In your Storage Lens dashboard, choose the **Bucket** tab\.

1. Scroll down to the **Buckets** section\. For **Metrics categories**, select **Detailed status code** metrics\. Then clear **Summary**\.

   The **Buckets** list updates to display all the available detailed status code metrics\. You can use this information to see which buckets have a large proportion of certain HTTP status codes and which status codes are common across buckets\. 

1. To filter the **Buckets** list to display only specific detailed status\-code metrics, choose the preferences icon \(![\[A screenshot that shows the preferences icon in the S3 Storage Lens dashboard.\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/preferences.png)\)\.

1. Clear the toggles for any detailed status\-code metrics that you don't want to view in the **Buckets** list\.

1. \(Optional\) Under **Page size**, choose the number of buckets to display in the list\.

1. Choose **Confirm**\.

   The **Buckets** list displays error count metrics for the number of buckets that you specified\. You can use this information to identify specific buckets that are experiencing many errors and troubleshoot errors by bucket\.

## Step 3: Troubleshoot errors<a name="storage-lens-detailed-status-code-step3"></a>

 After you identify buckets with a high proportion of specific HTTP status codes, you can troubleshoot these errors\. For more information, see the following:
+ [Why am I getting a 403 Forbidden error when I try to upload files in Amazon S3? ](http://aws.amazon.com/premiumsupport/knowledge-center/s3-403-forbidden-error/)
+ [Why am I getting a 403 Forbidden error when I try to modify a bucket policy in Amazon S3?](http://aws.amazon.com/premiumsupport/knowledge-center/s3-access-denied-bucket-policy/)
+ [How do I troubleshoot 403 Forbidden errors from my Amazon S3 bucket where all the resources are from the same AWS account?](http://aws.amazon.com/premiumsupport/knowledge-center/s3-troubleshoot-403-resource-same-account/)
+ [How do I troubleshoot an HTTP 500 or 503 error from Amazon S3?](http://aws.amazon.com/premiumsupport/knowledge-center/http-5xx-errors-s3/)