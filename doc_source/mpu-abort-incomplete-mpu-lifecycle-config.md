--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Configuring a bucket lifecycle policy to abort incomplete multipart uploads<a name="mpu-abort-incomplete-mpu-lifecycle-config"></a>

As a best practice, we recommend you configure a lifecycle rule using the `AbortIncompleteMultipartUpload` action to minimize your storage costs\. For more information about aborting a multipart upload, see [Aborting a multipart upload](abort-mpu.md)\.

Amazon S3 supports a bucket lifecycle rule that you can use to direct Amazon S3 to stop multipart uploads that don't complete within a specified number of days after being initiated\. When a multipart upload is not completed within the timeframe, it becomes eligible for an abort operation and Amazon S3 stops the multipart upload \(and deletes the parts associated with the multipart upload\)\.

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

In the example, the rule does not specify a value for the `Prefix` element \([object key name prefix](https://docs.aws.amazon.com/general/latest/gr/glos-chap.html#keyprefix)\)\. Therefore, it applies to all objects in the bucket for which you initiated multipart uploads\. Any multipart uploads that were initiated and did not complete within seven days become eligible for an abort operation\. The abort action has no effect on completed multipart uploads\.



For more information about the bucket lifecycle configuration, see [Managing your storage lifecycle](object-lifecycle-mgmt.md)\.

**Note**  
If the multipart upload is completed within the number of days specified in the rule, the `AbortIncompleteMultipartUpload` lifecycle action does not apply \(that is, Amazon S3 does not take any action\)\. Also, this action does not apply to objects\. No objects are deleted by this lifecycle action\.



The following `put-bucket-lifecycle-configuration` CLI command adds the lifecycle configuration for the specified bucket\. 

```
$ aws s3api put-bucket-lifecycle-configuration  \
        --bucket bucketname  \
        --lifecycle-configuration filename-containing-lifecycle-configuration
```

To test the CLI command, do the following:



1.  Set up the AWS CLI\. For instructions, see [Developing with Amazon S3 using the AWS CLI](setup-aws-cli.md)\. 

1.  Save the following example lifecycle configuration in a file \(lifecycle\.json\)\. The example configuration specifies empty prefix and therefore it applies to all objects in the bucket\. You can specify a prefix to restrict the policy to a subset of objects\.

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

1.  Run the following CLI command to set lifecycle configuration on your bucket\. 

   ```
   aws s3api put-bucket-lifecycle-configuration   \
   --bucket bucketname  \
   --lifecycle-configuration file://lifecycle.json
   ```

1.  To verify, retrieve the lifecycle configuration using the `get-bucket-lifecycle` CLI command\. 

   ```
   aws s3api get-bucket-lifecycle  \
   --bucket bucketname
   ```

1.  To delete the lifecycle configuration, use the `delete-bucket-lifecycle` CLI command\. 

   ```
   aws s3api delete-bucket-lifecycle \
   --bucket bucketname
   ```