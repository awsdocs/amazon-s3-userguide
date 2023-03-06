# Configuring a bucket lifecycle configuration to abort incomplete multipart uploads<a name="mpu-abort-incomplete-mpu-lifecycle-config"></a>

As a best practice, we recommend that you configure a lifecycle rule by using the `AbortIncompleteMultipartUpload` action to minimize your storage costs\. For more information about aborting a multipart upload, see [Aborting a multipart upload](abort-mpu.md)\.

Amazon S3 supports a bucket lifecycle rule that you can use to direct Amazon S3 to stop multipart uploads that aren't completed within a specified number of days after being initiated\. When a multipart upload isn't completed within the specified time frame, it becomes eligible for an abort operation\. Amazon S3 then stops the multipart upload and deletes the parts associated with the multipart upload\.

 The following is an example lifecycle configuration that specifies a rule with the `AbortIncompleteMultipartUpload` action\. 

```
<LifecycleConfiguration>
    <Rule>
        <ID>sample-rule</ID>
        <Prefix></Prefix>
        <Status>Enabled</Status>
        <AbortIncompleteMultipartUpload>
          <DaysAfterInitiation>7</DaysAfterInitiation>
        </AbortIncompleteMultipartUpload>
    </Rule>
</LifecycleConfiguration>
```

In the example, the rule doesn't specify a value for the `Prefix` element \(the [object key name prefix](https://docs.aws.amazon.com/general/latest/gr/glos-chap.html#keyprefix)\)\. Therefore, the rule applies to all objects in the bucket for which you initiated multipart uploads\. Any multipart uploads that were initiated and weren't completed within seven days become eligible for an abort operation\. The abort action has no effect on completed multipart uploads\.

For more information about the bucket lifecycle configuration, see [Managing your storage lifecycle](object-lifecycle-mgmt.md)\.

**Note**  
If the multipart upload is completed within the number of days specified in the rule, the `AbortIncompleteMultipartUpload` lifecycle action does not apply \(that is, Amazon S3 doesn't take any action\)\. Also, this action doesn't apply to objects\. No objects are deleted by this lifecycle action\. Additionally, you will not incur early delete charges for S3 Lifecycle when you remove any incomplete multipart upload parts\.

## Using the S3 console<a name="mpu-abort-incomplete-mpu-lifecycle-config-console"></a>

To automatically manage incomplete multipart uploads, you can use the S3 console to create a lifecycle rule to expire incomplete multipart upload bytes from your bucket after a specified number of days\. The following procedure shows you how to add a lifecycle rule to delete incomplete multipart uploads after 7 days\. For more information about adding lifecycle rules, see [Setting lifecycle configuration on a bucket](how-to-set-lifecycle-configuration-intro.md)\.

**To add a lifecycle rule to abort incomplete multipart uploads that are more than 7 days old**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets **list, choose the name of the bucket that you want to create a lifecycle rule for\.

1. Choose the **Management** tab, and choose **Create lifecycle rule**\.

1. In **Lifecycle rule name**, enter a name for your rule\.

   The name must be unique within the bucket\. 

1. Choose the scope of the lifecycle rule:
   + To create a lifecycle rule for all objects with a specific prefix, choose **Limit the scope of this rule using one or more filters**, and enter the prefix in the **Prefix **field\.
   + To create a lifecycle rule for all objects in the bucket, choose **This rule applies to **all** objects in the bucket**, and choose **I acknowledge that this rule applies to all objects in the bucket**\.

1. Under **Lifecycle rule actions**, select **Delete expired object delete markers or incomplete multipart uploads**\.

1. Under **Delete expired object delete markers or incomplete multipart uploads**, select **Delete incomplete multipart uploads**\.

1. In the **Number of days **field, enter the number of days after which to delete incomplete multipart uploads \(for this example, 7 days\)\. 

1. Choose **Create rule**\.

## Using the AWS CLI<a name="mpu-abort-incomplete-mpu-lifecycle-config-cli"></a>

The following `put-bucket-lifecycle-configuration` AWS Command Line Interface \(AWS CLI\) command adds the lifecycle configuration for the specified bucket\. To use this command, replace the `user input placeholders` with your information\.

```
aws s3api put-bucket-lifecycle-configuration  \
        --bucket DOC-EXAMPLE-BUCKET1  \
        --lifecycle-configuration filename-containing-lifecycle-configuration
```

The following example shows how to add a lifecycle rule to abort incomplete multipart uploads by using the AWS CLI\. It includes an example JSON lifecycle configuration to abort incomplete multipart uploads that are more than 7 days old\.

To use the CLI commands in this example, replace the `user input placeholders` with your information\.

**To add a lifecycle rule to abort incomplete multipart uploads**

1. Set up the AWS CLI\. For instructions, see [Developing with Amazon S3 using the AWS CLI](setup-aws-cli.md)\. 

1. Save the following example lifecycle configuration in a file \(for example, *`lifecycle.json`*``\)\. This example configuration specifies an empty prefix, and therefore it applies to all objects in the bucket\. To restrict the configuration to a subset of objects, you can specify a prefix\.

   ```
   {
       "Rules": [
           {
               "ID": "Test Rule",
               "Status": "Enabled",
               "Filter": {
                   "Prefix": ""
               },
               "AbortIncompleteMultipartUpload": {
                   "DaysAfterInitiation": 7
               }
           }
       ]
   }
   ```

1.  Run the following CLI command to set this lifecycle configuration on your bucket\. 

   ```
   aws s3api put-bucket-lifecycle-configuration   \
   --bucket DOC-EXAMPLE-BUCKET1  \
   --lifecycle-configuration file://lifecycle.json
   ```

1.  To verify that the lifecycle configuration has been set on your bucket, retrieve the lifecycle configuration by using the following `get-bucket-lifecycle` command\. 

   ```
   aws s3api get-bucket-lifecycle  \
   --bucket DOC-EXAMPLE-BUCKET1
   ```

1.  To delete the lifecycle configuration, use the following `delete-bucket-lifecycle` command\. 

   ```
   aws s3api delete-bucket-lifecycle \
   --bucket DOC-EXAMPLE-BUCKET1
   ```