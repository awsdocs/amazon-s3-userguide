# Enabling CloudWatch publishing for S3 Storage Lens<a name="storage-lens-cloudwatch-enable-publish-option"></a>

You can publish S3 Storage Lens usage and activity metrics to Amazon CloudWatch to create a unified view of your operational health in CloudWatch [dashboards](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/CloudWatch_Dashboards.html)\. You can also use CloudWatch features, like alarms and triggered actions, metric math, and anomaly detection, to monitor and take action on S3 Storage Lens metrics\. In addition, the CloudWatch APIs enable applications, including third\-party providers, to access your S3 Storage Lens metrics\. For more information about CloudWatch features, see the [https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_concepts.html](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/cloudwatch_concepts.html)\.

S3 Storage Lens metrics are published to CloudWatch in the account that owns the S3 Storage Lens configuration\. After you enable the CloudWatch publishing option within advanced metrics and recommendations, you can access organization, account, and bucket\-level usage and activity metrics in CloudWatch\. Prefix\-level metrics are not available in CloudWatch\.

You can enable CloudWatch support for new or existing dashboard configurations using the S3 console, Amazon S3 REST APIs, AWS CLI, and AWS SDKs\. The CloudWatch publishing option is available for dashboards upgraded to S3 Storage Lens *advanced metrics and recommendations\. *For S3 Storage Lens* advanced metrics and recommendations *pricing, see [Amazon S3 pricing](http://aws.amazon.com/s3/pricing/)\. No additional CloudWatch metrics publishing charges apply; however, other CloudWatch charges such as dashboards, alarms, and API are applicable\.

To enable the CloudWatch publishing option for S3 Storage Lens metrics, see the following topics\.

**Note**  
S3 Storage Lens metrics are daily metrics and are published to CloudWatch once per day\. When you query S3 Storage Lens metrics in CloudWatch, the period for the query must be 1 day \(86400 seconds\)\. After your daily S3 Storage Lens metrics appear in your S3 Storage Lens dashboard in the Amazon S3 console, it can take a few hours for these same metrics to appear in CloudWatch\. When you enable the CloudWatch publishing option for S3 Storage Lens metrics for the first time, it can take up to 24 hours for your metrics to publish to CloudWatch\.   
Currently, S3 Storage Lens metrics cannot be consumed via CloudWatch streams\. 

## Using the S3 console<a name="storage-lens-cloudwatch-enable-publish-console"></a>

When you update a S3 Storage Lens dashboard, you can’t change the dashboard name or home Region\. You also can’t change the scope of the default dashboard, which is scoped to your entire account’s storage\.

**To update S3 Storage Lens dashboard to enable CloudWatch publishing**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **S3 Storage Lens**\.

1. Choose the dashboard that you want to edit, and then choose **Edit\.**

1. Under **Metrics selection**, choose **Advanced metrics and recommendations**\.

   Advanced metrics and recommendations are available for an additional charge\. Advanced metrics and recommendations include a 15\-month period for data queries, usage metrics aggregated at the prefix level, activity metrics aggregated by bucket, the CloudWatch publishing option, and contextual recommendations that help you optimize storage costs and apply data protection best practices\. For more information, see [Amazon S3 pricing](http://aws.amazon.com/s3/pricing/)\.

1. Under **Select Advanced metrics and recommendations features**, select **CloudWatch publishing**\.
**Important**  
If your configuration enables prefix aggregation for usage metrics, prefix\-level metrics will not be published to CloudWatch\. Only bucket, account, and organization\-level S3 Storage Lens metrics are published to CloudWatch\.

1. Choose **Create dashboard**\.

**To create a new S3 Storage Lens dashboard that enables CloudWatch support**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Storage Lens**\. 

1. Choose **Create dashboard**\. 

1. Under **General**, define configuration options:

   1. In **Dashboard name**, enter your dashboard name\.

      Dashboard names must be fewer than 65 characters and must not contain special characters or spaces\. You can’t change the dashboard name after you create your dashboard\.

   1. Choose the **Home Region ** for your dashboard\.

      Metrics for all Regions included in this dashboard scope are stored centrally in the designated home Region\. In CloudWatch, S3 Storage Lens metrics are also available in the home Region\. You can’t change the home Region after you create your dashboard\.

1. \(Optional\) To add tags, choose **Add tag ** and enter the tag **Key** and **Value**\.
**Note**  
You can add up to 50 tags to your dashboard configuration\.

1. Define the scope for your configuration:

   1. If you’re creating an organization\-level configuration, choose the accounts to include in the configuration: **Include all accounts in your configuration** or **Limit the scope to your signed\-in account**\.
**Note**  
When you create an organization\-level configuration that includes all accounts, you can only include or exclude Regions and not buckets\.

   1. Choose Regions and buckets that you want S3 Storage Lens to include in the dashboard configuration:
      + To include all Regions, choose **Include Regions and buckets**\.
      + To include specific Regions, clear **Include all Regions**\. Under **Choose Regions to include**, choose the Regions that you want S3 Storage Lens to include in the dashboard\.
      + To include specific buckets, clear **Include all buckets**\. Under **Choose buckets to include**, choose the buckets that you want S3 Storage Lens to include in the dashboard\. 
**Note**  
You can choose up to 50 buckets\.

1. In the **Metrics selection**, choose **Advanced metrics and recommendations**\.

   For more information about advanced metrics and recommendations pricing, see [Amazon S3 pricing](http://aws.amazon.com/s3/pricing/)\. 

1. Under **Select advanced metrics and recommendations features**, select the options that apply:
   + **Activity metrics**
   + **CloudWatch publishing**
**Important**  
If you enable prefix aggregation for your S3 Storage Lens configuration, prefix\-level metrics will not be published to CloudWatch\. Only bucket, account, and organization\-level S3 Storage Lens metrics are published to CloudWatch
   + **Prefix aggregation**

1. \(Optional\) Configure metrics export\.

   For more information about how to configure a metrics export, see step [Creating an Amazon S3 Storage Lens dashboard](storage_lens_console_creating.md)\.

1. Choose **Create dashboard**\.

## Using the AWS CLI<a name="storage-lens-cloudwatch-enable-publish-cli"></a>

The following AWS CLI example enables the CloudWatch publishing option using a S3 Storage Lens organization\-level *advanced metrics and recommendations* configuration\. 

```
aws s3control put-storage-lens-configuration --account-id=EXAMPLE-AWS-ACCOUNT-ID --config-id=your-configuration-id --region=us-east-1 --storage-lens-configuration=file://./config.json

config.json
{
    "Id": "SampleS3StorageLensConfiguration", //Use this property to identify S3 S3 Storage Lens configuration.
    "AwsOrg": { //Use this property when enabling S3 S3 Storage Lens for AWS Organizations
        "Arn": "arn:aws:organizations::222222222222:organization/o-abcdefgh"
    },
    "AccountLevel": {
        "ActivityMetrics": {
            "IsEnabled": true
        },
        "BucketLevel": {
            "ActivityMetrics": { //Enables Activity Metrics
                "IsEnabled": true
            },
            "PrefixLevel": { //Enables Prefix Level Metrics
                "StorageMetrics": {
                    "IsEnabled": true,
                    "SelectionCriteria": {
                        "MaxDepth": 5,
                        "MinStorageBytesPercentage": 1.25,
                        "Delimiter": "/"
                    }
                }
            }
        }
    },
    "Exclude": { //Replace with include if you prefer to include Regions.
        "Regions": [
            "eu-west-1"
        ],
        "Buckets": [ //This attribute is not supported for organization-level configurations.
            "arn:aws:s3:::source_bucket1"
        ]
    },
    "IsEnabled": true, //Whether the configuration is enabled
    "DataExport": {
        "CloudWatchMetrics": { //Enables publishing Lens metrics to CloudWatch
            "IsEnabled": true
        },
        "S3BucketDestination": { //Enable export of metrics report to na S3 bucket
            "OutputSchemaVersion": "V_1",
            "Format": "CSV", //You can add "Parquet" if you prefer.
            "AccountId": "ExampleAWSAccountNo8",
            "Arn": "arn:aws:s3:::destination-bucket-name", //The destination bucket for your metrics export must be in the same Region as your S3 S3 Storage Lens configuration.
            "Prefix": "prefix-for-your-export-destination",
            "Encryption": {
                "SSES3": {}
            }
        }
    }
}
```

## Using the AWS SDK for Java<a name="storage-lens-cloudwatch-enable-publish-sdk"></a>

```
package aws.example.s3control;

import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.services.s3control.AWSS3Control;
import com.amazonaws.services.s3control.AWSS3ControlClient;
import com.amazonaws.services.s3control.model.AccountLevel;
import com.amazonaws.services.s3control.model.ActivityMetrics;
import com.amazonaws.services.s3control.model.BucketLevel;
import com.amazonaws.services.s3control.model.CloudWatchMetrics;
import com.amazonaws.services.s3control.model.Format;
import com.amazonaws.services.s3control.model.Include;
import com.amazonaws.services.s3control.model.OutputSchemaVersion;
import com.amazonaws.services.s3control.model.PrefixLevel;
import com.amazonaws.services.s3control.model.PrefixLevelStorageMetrics;
import com.amazonaws.services.s3control.model.PutStorageLensConfigurationRequest;
import com.amazonaws.services.s3control.model.S3BucketDestination;
import com.amazonaws.services.s3control.model.SSES3;
import com.amazonaws.services.s3control.model.SelectionCriteria;
import com.amazonaws.services.s3control.model.StorageLensAwsOrg;
import com.amazonaws.services.s3control.model.StorageLensConfiguration;
import com.amazonaws.services.s3control.model.StorageLensDataExport;
import com.amazonaws.services.s3control.model.StorageLensDataExportEncryption;
import com.amazonaws.services.s3control.model.StorageLensTag;

import java.util.Arrays;
import java.util.List;

import static com.amazonaws.regions.Regions.US_WEST_2;

public class CreateAndUpdateDashboard {

    public static void main(String[] args) {
        String configurationId = "ConfigurationId";
        String sourceAccountId = "Source Account ID";
        String exportAccountId = "Destination Account ID";
        String exportBucketArn = "arn:aws:s3:::destBucketName"; // The destination bucket for your metrics export must be in the same Region as your S3 S3 Storage Lens configuration.
        String awsOrgARN = "arn:aws:organizations::222222222222:organization/o-abcdefgh";
        Format exportFormat = Format.CSV;

        try {
            SelectionCriteria selectionCriteria = new SelectionCriteria()
                    .withDelimiter("/")
                    .withMaxDepth(5)
                    .withMinStorageBytesPercentage(10.0);
            PrefixLevelStorageMetrics prefixStorageMetrics = new PrefixLevelStorageMetrics()
                    .withIsEnabled(true)
                    .withSelectionCriteria(selectionCriteria);
            BucketLevel bucketLevel = new BucketLevel()
                    .withActivityMetrics(new ActivityMetrics().withIsEnabled(true))
                    .withPrefixLevel(new PrefixLevel().withStorageMetrics(prefixStorageMetrics));
            AccountLevel accountLevel = new AccountLevel()
                    .withActivityMetrics(new ActivityMetrics().withIsEnabled(true))
                    .withBucketLevel(bucketLevel);

            Include include = new Include()
                    .withBuckets(Arrays.asList("arn:aws:s3:::bucketName"))
                    .withRegions(Arrays.asList("us-west-2"));

            StorageLensDataExportEncryption exportEncryption = new StorageLensDataExportEncryption()
                    .withSSES3(new SSES3());
            S3BucketDestination s3BucketDestination = new S3BucketDestination()
                    .withAccountId(exportAccountId)
                    .withArn(exportBucketArn)
                    .withEncryption(exportEncryption)
                    .withFormat(exportFormat)
                    .withOutputSchemaVersion(OutputSchemaVersion.V_1)
                    .withPrefix("Prefix");
            CloudWatchMetrics cloudWatchMetrics = new CloudWatchMetrics()
                    .withIsEnabled(true);
            StorageLensDataExport dataExport = new StorageLensDataExport()
                    .withCloudWatchMetrics(cloudWatchMetrics)
                    .withS3BucketDestination(s3BucketDestination);

            StorageLensAwsOrg awsOrg = new StorageLensAwsOrg()
                    .withArn(awsOrgARN);

            StorageLensConfiguration configuration = new StorageLensConfiguration()
                    .withId(configurationId)
                    .withAccountLevel(accountLevel)
                    .withInclude(include)
                    .withDataExport(dataExport)
                    .withAwsOrg(awsOrg)
                    .withIsEnabled(true);

            List<StorageLensTag> tags = Arrays.asList(
                    new StorageLensTag().withKey("key-1").withValue("value-1"),
                    new StorageLensTag().withKey("key-2").withValue("value-2")
            );

            AWSS3Control s3ControlClient = AWSS3ControlClient.builder()
                    .withCredentials(new ProfileCredentialsProvider())
                    .withRegion(US_WEST_2)
                    .build();

            s3ControlClient.putStorageLensConfiguration(new PutStorageLensConfigurationRequest()
                    .withAccountId(sourceAccountId)
                    .withConfigId(configurationId)
                    .withStorageLensConfiguration(configuration)
                    .withTags(tags)
            );
        } catch (AmazonServiceException e) {
            // The call was transmitted successfully, but Amazon S3 couldn't process
            // it and returned an error response.
            e.printStackTrace();
        } catch (SdkClientException e) {
            // Amazon S3 couldn't be contacted for a response, or the client
            // couldn't parse the response from Amazon S3.
            e.printStackTrace();
        }
    }
}
```

## Using the REST API<a name="storage-lens-cloudwatch-enable-publish-api"></a>

To enable the CloudWatch publishing option using the Amazon S3 REST API, you can use [PutStorageLensConfiguration](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutStorageLensConfiguration.html)\.

**Next steps**  
After you enable the CloudWatch publishing option, you can access your S3 Storage Lens metrics in CloudWatch\. You also can leverage CloudWatch features to monitor and analyze your S3 Storage Lens data in CloudWatch\. For more information, see the following topics:
+ [S3 Storage Lens metrics and dimensions](storage-lens-cloudwatch-metrics-dimensions.md)
+ [Working with S3 Storage Lens metrics in CloudWatch](storage-lens-cloudwatch-monitoring-cloudwatch.md)