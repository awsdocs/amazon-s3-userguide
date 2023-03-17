# Viewing Amazon S3 Multi\-Region Access Points configuration details<a name="multi-region-access-point-view-examples"></a>

The following example demonstrates how to view Multi\-Region Access Point configuration details by using the Amazon S3 console\. 

## Using the S3 console<a name="multi-region-access-point-view-console"></a>

**To create a Multi\-Region Access Point**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Multi\-Region Access Points**\.

1. Choose the name of the Multi\-Region Access Point for which you want to view the configuration details\.
   + The **Properties** tab lists all of the buckets that are associated with your Multi\-Region Access Point, the creation date, the Amazon Resource Name \(ARN\), and the alias\. The AWS account ID column also lists any buckets owned by external accounts that are associated with your Multi\-Region Access Point\.
   + The **Permissions** tab lists the Block Public Access settings that are applied to the buckets associated with this Multi\-Region Access Point\. You can also view the Multi\-Region Access Point policy for your Multi\-Region Access Point, if you’ve created one\. The **Info** alert on the **Permissions** page also lists all the buckets \(in your account and other accounts\) for this Multi\-Region Access Point that have the **Public Access is blocked** setting enabled\.
   + The **Replication and failover** tab provides a map view of the buckets that are associated with your Multi\-Region Access Point and the Regions that the buckets reside int\. If there are buckets from another account that you don’t have permission to pull data from, the Region will be marked in red on the **Replication summary** map, indicating that it is an **AWS Region with errors getting replication status**\.
**Note**  
To retrieve replication status information from a bucket in an external account, the bucket owner must grant you the `s3:GetBucketReplication` permission in their bucket policy\.

     This tab also provides the replication metrics, replication rules, and failover statuses for the Regions that are used with your Multi\-Region Access Point\.

## Using the AWS CLI<a name="multi-region-access-point-view-cli"></a>

 You can use the AWS CLI to view the configuration details for a Multi\-Region Access Point\.

The following AWS CLI example gets your current Multi\-Region Access Point configuration\. To use this example command, replace the `user input placeholders` with your own information\.

```
aws s3control get-multi-region-access-point --account-id 111122223333 --name DOC-EXAMPLE-BUCKET1
```