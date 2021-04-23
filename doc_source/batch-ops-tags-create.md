# Creating a Batch Operations job with job tags used for labeling<a name="batch-ops-tags-create"></a>

You can label and control access to your S3 Batch Operations jobs by adding *tags*\. Tags can be used to identify who is responsible for a Batch Operations job\. You can create jobs with tags attached to them, and you can add tags to jobs after they are created\. For more information, see [Controlling access and labeling jobs using tags](batch-ops-job-tags.md)\.

## Using the AWS CLI<a name="batch-ops-example-cli-job-tags-create-job"></a>

The following AWS CLI example creates an S3 Batch Operations `S3PutObjectCopy` job using job tags as labels for the job\. 

1. Select the action or `OPERATION` that you want the Batch Operations job to perform, and choose your `TargetResource`\.

   ```
   read -d '' OPERATION <<EOF
   {
     "S3PutObjectCopy": {
       "TargetResource": "arn:aws:s3:::destination-bucket"
     }
   }
   EOF
   ```

1. Identify the job `TAGS` that you want for the job\. In this case, you apply two tags, `department` and `FiscalYear`, with the values `Marketing` and `2020` respectively\.

   ```
   read -d '' TAGS <<EOF
   [
     {
       "Key": "department",
       "Value": "Marketing"
     },
     {
       "Key": "FiscalYear",
       "Value": "2020"
     }
   ]
   EOF
   ```

1. Specify the `MANIFEST` for the Batch Operations job\.

   ```
   read -d '' MANIFEST <<EOF
   {
     "Spec": {
       "Format": "EXAMPLE_S3BatchOperations_CSV_20180820",
       "Fields": [
         "Bucket",
         "Key"
       ]
     },
     "Location": {
       "ObjectArn": "arn:aws:s3:::example-bucket/example_manifest.csv",
       "ETag": "example-5dc7a8bfb90808fc5d546218"
     }
   }
   EOF
   ```

1. Configure the `REPORT` for the Batch Operations job\.

   ```
   read -d '' REPORT <<EOF
   {
     "Bucket": "arn:aws:s3:::example-report-bucket",
     "Format": "Example_Report_CSV_20180820",
     "Enabled": true,
     "Prefix": "reports/copy-with-replace-metadata",
     "ReportScope": "AllTasks"
   }
   EOF
   ```

1. Run the`create-job` action to create your Batch Operations job with inputs set in the preceding steps\.

   ```
   aws \
       s3control create-job \
       --account-id 123456789012 \
       --manifest "${MANIFEST//$'\n'}" \
       --operation "${OPERATION//$'\n'/}" \
       --report "${REPORT//$'\n'}" \
       --priority 10 \
       --role-arn arn:aws:iam::123456789012:role/batch-operations-role \
       --tags "${TAGS//$'\n'/}" \
       --client-request-token "$(uuidgen)" \
       --region us-west-2 \
       --description "Copy with Replace Metadata";
   ```

## Using the AWS SDK Java<a name="batch-ops-examples-java-job-with-tags-create"></a>

**Example**  
The following example creates an S3 Batch Operations job with tags using the AWS SDK for Java\.  

```
public String createJob(final AWSS3ControlClient awss3ControlClient) {
    final String manifestObjectArn = "arn:aws:s3:::example-manifest-bucket/manifests/10_manifest.csv";
    final String manifestObjectVersionId = "example-5dc7a8bfb90808fc5d546218";

    final JobManifestLocation manifestLocation = new JobManifestLocation()
            .withObjectArn(manifestObjectArn)
            .withETag(manifestObjectVersionId);

    final JobManifestSpec manifestSpec =
            new JobManifestSpec().withFormat(JobManifestFormat.S3InventoryReport_CSV_20161130);

    final JobManifest manifestToPublicApi = new JobManifest()
            .withLocation(manifestLocation)
            .withSpec(manifestSpec);

    final String jobReportBucketArn = "arn:aws:s3:::example-report-bucket";
    final String jobReportPrefix = "example-job-reports";

    final JobReport jobReport = new JobReport()
            .withEnabled(true)
            .withReportScope(JobReportScope.AllTasks)
            .withBucket(jobReportBucketArn)
            .withPrefix(jobReportPrefix)
            .withFormat(JobReportFormat.Report_CSV_20180820);

    final String lambdaFunctionArn = "arn:aws:lambda:us-west-2:123456789012:function:example-function";

    final JobOperation jobOperation = new JobOperation()
            .withLambdaInvoke(new LambdaInvokeOperation().withFunctionArn(lambdaFunctionArn));

    final S3Tag departmentTag = new S3Tag().withKey("department").withValue("Marketing");
    final S3Tag fiscalYearTag = new S3Tag().withKey("FiscalYear").withValue("2020");

    final String roleArn = "arn:aws:iam::123456789012:role/example-batch-operations-role";
    final Boolean requiresConfirmation = true;
    final int priority = 10;

    final CreateJobRequest request = new CreateJobRequest()
            .withAccountId("123456789012")
            .withDescription("Test lambda job")
            .withManifest(manifestToPublicApi)
            .withOperation(jobOperation)
            .withPriority(priority)
            .withRoleArn(roleArn)
            .withReport(jobReport)
            .withTags(departmentTag, fiscalYearTag)
            .withConfirmationRequired(requiresConfirmation);

    final CreateJobResult result = awss3ControlClient.createJob(request);

    return result.getJobId();
}
```