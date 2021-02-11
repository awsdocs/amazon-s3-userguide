--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Deleting a bucket<a name="delete-bucket"></a>

You can delete an empty Amazon S3 bucket, and when you're using the AWS Management Console, you can delete a bucket that contains objects\. If you delete a bucket that contains objects, all the objects in the bucket are permanently deleted\. 

When you delete a bucket that has S3 Bucket Versioning enabled, all versions of all the objects in the bucket are permanently deleted\. For more information about versioning, see [Working with objects in a versioning\-enabled bucket](manage-objects-versioned-bucket.md)\.

Before deleting a bucket, consider the following:
+ Bucket names are unique\. If you delete a bucket, another AWS user can use the name\. 
+ When you delete a bucket that contains objects, all the objects in the bucket are permanently deleted, including objects that transitioned to the `S3 Glacier` storage class\.
+ If the bucket hosts a static website, and you created and configured an Amazon Route 53 hosted zone as described in [Configuring a static website using a custom domain registered with Route 53](website-hosting-custom-domain-walkthrough.md), you must clean up the Route 53 hosted zone settings that are related to the bucket\. For more information, see [Step 2: Delete the Route 53 hosted zone](getting-started-cleanup.md#getting-started-cleanup-route53)\.
+ If the bucket receives log data from Elastic Load Balancing \(ELB\): We recommend that you stop the delivery of ELB logs to the bucket before deleting it\. After you delete the bucket, if another user creates a bucket using the same name, your log data could potentially be delivered to that bucket\. For information about ELB access logs, see [Access logs](https://docs.aws.amazon.com/elasticloadbalancing/latest/classic/access-log-collection.html) in the *User Guide for Classic Load Balancers* and [Access logs](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/load-balancer-access-logs.html) in the *User Guide for Application Load Balancers*\.

**Important**  
Bucket names are unique\. If you delete a bucket, another AWS user can use the name\. If you want to continue to use the same bucket name, don't delete the bucket\. We recommend that you empty the bucket and keep it\.

## Using the S3 console<a name="delete-bucket-console"></a>

**To delete an S3 bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, select the option next to the name of the bucket that you want to delete, and then choose **Delete** at the top of the page\.

1. On the **Delete bucket** page, confirm that you want to delete the bucket by entering the bucket name into the text field, and then choose **Delete bucket**\.
**Note**  
If the bucket contains any objects, empty the bucket before deleting it by selecting the *empty bucket configuration* link in the **This bucket is not empty** error alert and following the instructions on the **Empty bucket** page\. Then return to the **Delete bucket** page and delete the bucket\.

   

## Using the AWS CLI<a name="delete-bucket-awscli"></a>

You can delete a bucket that contains objects with the AWS CLI if it doesn't have versioning enabled\. When you delete a bucket that contains objects, all the objects in the bucket are permanently deleted, including objects that are transitioned to the `S3 Glacier` storage class\.

If your bucket does not have versioning enabled, you can use the `rb` \(remove bucket\) AWS CLI command with the `--force` parameter to delete the bucket and all the objects in it\. This command deletes all objects first and then deletes the bucket\.

```
$ aws s3 rb s3://bucket-name --force  
```

For more information, see [Using High\-Level S3 Commands with the AWS Command Line Interface](https://docs.aws.amazon.com/cli/latest/userguide/using-s3-commands.html) in the AWS Command Line Interface User Guide\.