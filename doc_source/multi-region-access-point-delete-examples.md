# Deleting a Multi\-Region Access Point<a name="multi-region-access-point-delete-examples"></a>

The following procedure explains how to delete a Multi\-Region Access Point by using the Amazon S3 console\.

Deleting a Multi\-Region Access Point does not delete the buckets associated with the Multi\-Region Access Point, only the Multi\-Region Access Point itself\. 

## Using the S3 console<a name="multi-region-access-point-delete-console"></a>

**To delete a Multi\-Region Access Point**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Multi\-Region Access Points**\.

1. Select the option button next to the name of your Multi\-Region Access Point\.

1. Choose **Delete**\.

1. In the **Delete Multi\-Region Access Point** dialog box, enter the name of the AWS bucket that you want to delete\.
**Note**  
Make sure to enter a valid bucket name\. Otherwise, the **Delete** button will be disabled\.

1. Choose **Delete** to confirm deletion of your Multi\-Region Access Point\.

## Using the AWS CLI<a name="multi-region-access-point-delete-cli"></a>

You can use the AWS CLI to delete a Multi\-Region Access Point\. This action does not delete the buckets associated with the Multi\-Region Access Point, only the Multi\-Region Access Point itself\. To use this example command, replace the `user input placeholders` with your own information\.

```
aws s3control delete-multi-region-access-point --details Name=example-multi-region-access-point-name
```