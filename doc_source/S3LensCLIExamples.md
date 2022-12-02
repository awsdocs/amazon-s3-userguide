# Amazon S3 Storage Lens examples using the AWS CLI<a name="S3LensCLIExamples"></a>

S3 Storage Lens aggregates your metrics and displays the information in the **Account snapshot** section on the Amazon S3 console **Buckets** page\. S3 Storage Lens also provides an interactive dashboard that you can use to visualize insights and trends, flag outliers, and receive recommendations for optimizing storage costs and applying data\-protection best practices\. Your dashboard has drill\-down options to generate insights at the organization, account, bucket, object, or prefix level\. You can also send a once\-daily metrics export in CSV or Parquet format to an S3 bucket\. For more information, see [Assessing storage activity and usage with Amazon S3 Storage Lens](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage_lens.html)\. 

The following examples show how you can use S3 Storage Lens with the AWS Command Line Interface\.

**Topics**
+ [Helper files for using Amazon S3 Storage Lens](#S3LensHelperFilesCLI)
+ [Using Amazon S3 Storage Lens configurations with the AWS CLI](#S3LensConfigurationsCLI)
+ [Using Amazon S3 Storage Lens with AWS Organizations examples using the AWS CLI](#S3LensOrganizationsCLI)

## Helper files for using Amazon S3 Storage Lens<a name="S3LensHelperFilesCLI"></a>

Use the following JSON files for key inputs for your examples\.



### S3 Storage Lens sample configuration JSON<a name="S3LensHelperFilesSampleConfigurationCLI"></a>

**Example `config.json`**  
The `config.json` file contains the details of a S3 Storage Lens Organizations\-level *advanced metrics and recommendations* configuration\. To use the following example, replace the `user input placeholders` with your own information\.  
Additional charges apply for advanced metrics and recommendations\. For more information, see [advanced metrics and recommendations](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage_lens_basics_metrics_recommendations.html#storage_lens_basics_metrics_selection)\.

```
{
  "Id": "SampleS3StorageLensConfiguration", //Use this property to identify your S3 Storage Lens configuration.
  "AwsOrg": { //Use this property when enabling S3 Storage Lens for AWS Organizations.
    "Arn": "arn:aws:organizations::123456789012:organization/o-abcdefgh"
  },
  "AccountLevel": {
    "ActivityMetrics": {
      "IsEnabled":true
    },
    "AdvancedCostOptimizationMetrics": {
      "IsEnabled":true
    },
    "AdvancedDataProtectionMetrics": {
      "IsEnabled":true
    },
    "DetailedStatusCodesMetrics": {
      "IsEnabled":true
    },
    "BucketLevel": {
      "ActivityMetrics": {
        "IsEnabled":true //Mark this as false if you want only free metrics.
      },
      "ActivityMetrics": {
        "IsEnabled":true //Mark this as false if you want only free metrics.
      },
      "AdvancedCostOptimizationMetrics": {
        "IsEnabled":true //Mark this as false if you want only free metrics.
      },
      "DetailedStatusCodesMetrics": {
        "IsEnabled":true //Mark this as false if you want only free metrics.
      },
      "PrefixLevel":{
        "StorageMetrics":{
          "IsEnabled":true, //Mark this as false if you want only free metrics.
          "SelectionCriteria":{
            "MaxDepth":5,
            "MinStorageBytesPercentage":1.25,
            "Delimiter":"/"
          }
        }
      }
    }
  },
  "Exclude": { //Replace with "Indlude" if you prefer to include Regions.
    "Regions": [
      "eu-west-1"
    ],
    "Buckets": [ //This attribute is not supported for AWS Organizations-level configurations.
      "arn:aws:s3:::source_bucket1"
    ]
  },
  "IsEnabled": true, //Whether the configuration is enabled
  "DataExport": { //Details about the metrics export
    "S3BucketDestination": {
      "OutputSchemaVersion": "V_1",
      "Format": "CSV", //You can add "Parquet" if you prefer.
      "AccountId": "111122223333",
      "Arn": "arn:aws:s3:::destination-bucket-name", // The destination bucket for your metrics export must be in the same Region as your S3 Storage Lens configuration. 
      "Prefix": "prefix-for-your-export-destination",
      "Encryption": {
        "SSES3": {}
      }
    },
    "CloudWatchMetrics": {
      "IsEnabled": true //Mark this as false if you don't want to enable the Amazon CloudWatch publishing option.
    }
  }
}
```

### S3 Storage Lens sample configuration tags JSON<a name="S3LensHelperFilesSampleConfigurationTagsCLI"></a>

**Example `tags.json`**  
The `tags.json` file contains the tags that you want to apply to your S3 Storage Lens configuration\. To use this example, replace the `user input placeholders` with your own information\.  

```
[
    {
        "Key": "key1",
        "Value": "value1"
    },
    {
        "Key": "key2",
        "Value": "value2"
    }
]
```

### S3 Storage Lens sample configuration IAM permissions<a name="S3LensHelperFilesSampleConfigurationIAMPermissionsCLI"></a>

**Example `permissions.json` – Specific dashboard name**  
This example policy shows an S3 Storage Lens IAM `permissions.json` file with a specific dashboard name specified\. Replace *`value1`*, `us-east-1`, `your-dashboard-name`, and `example-account-id` with your own values\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetStorageLensConfiguration",
                "s3:DeleteStorageLensConfiguration",
                "s3:PutStorageLensConfiguration"
            ],
            "Condition": {
                "StringEquals": {
                    "aws:ResourceTag/key1": "value1"
                }
            },
            "Resource": "arn:aws:s3:us-east-1:example-account-id:storage-lens/your-dashboard-name"
        }
    ]
}
```

**Example `permissions.json` – No specific dashboard name**  
This example policy shows an S3 Storage Lens IAM `permissions.json` file without a specific dashboard name specified\. Replace *`value1`*, `us-east-1`, and `example-account-id` with your own values\.  

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetStorageLensConfiguration",
                "s3:DeleteStorageLensConfiguration",
                "s3:PutStorageLensConfiguration"
            ],
            "Condition": {
                "StringEquals": {
                    "aws:ResourceTag/key1": "value1"
                }
            },
            "Resource": "arn:aws:s3:us-east-1:example-account-id:storage-lens/*"
        }
    ]
}
```

## Using Amazon S3 Storage Lens configurations with the AWS CLI<a name="S3LensConfigurationsCLI"></a>

You can use the AWS CLI to list, create, delete, get, tag, and update your S3 Storage Lens configurations\. The following examples use the helper JSON files for key inputs\. To use these examples, replace the `user input placeholders` with your own information\.

**Topics**
+ [Create an S3 Storage Lens configuration](#S3PutStorageLensConfigurationTagsCLI)
+ [Create an S3 Storage Lens configuration without tags](#S3PutStorageLensConfigurationWOTagsCLI)
+ [Get an S3 Storage Lens configuration](#S3GetStorageLensConfigurationCLI)
+ [List S3 Storage Lens configurations without a next token](#S3ListStorageLensConfigurationsWOTokenCLI)
+ [List S3 Storage Lens configurations](#S3ListStorageLensConfigurationsCLI)
+ [Delete an S3 Storage Lens configuration](#S3DeleteStorageLensConfigurationCLI)
+ [Add tags to an S3 Storage Lens configuration](#S3PutStorageLensConfigurationTaggingCLI)
+ [Get tags for an S3 Storage Lens configuration](#S3GetStorageLensConfigurationTaggingCLI)
+ [Delete tags for an S3 Storage Lens configuration](#S3DeleteStorageLensConfigurationTaggingCLI)

### Create an S3 Storage Lens configuration<a name="S3PutStorageLensConfigurationTagsCLI"></a>

**Example Create an S3 Storage Lens configuration**  

```
aws s3control put-storage-lens-configuration --account-id=222222222222 --config-id=your-configuration-id --region=us-east-1 --storage-lens-configuration=file://./config.json --tags=file://./tags.json
```

### Create an S3 Storage Lens configuration without tags<a name="S3PutStorageLensConfigurationWOTagsCLI"></a>

**Example Create an S3 Storage Lens configuration without tags**  

```
aws s3control put-storage-lens-configuration --account-id=222222222222 --config-id=your-configuration-id --region=us-east-1 --storage-lens-configuration=file://./config.json
```

### Get an S3 Storage Lens configuration<a name="S3GetStorageLensConfigurationCLI"></a>

**Example Get an S3 Storage Lens configuration**  

```
aws s3control get-storage-lens-configuration --account-id=222222222222 --config-id=your-configuration-id --region=us-east-1
```

### List S3 Storage Lens configurations without a next token<a name="S3ListStorageLensConfigurationsWOTokenCLI"></a>

**Example List S3 Storage Lens configurations without a next token**  

```
aws s3control list-storage-lens-configurations --account-id=222222222222 --region=us-east-1
```

### List S3 Storage Lens configurations<a name="S3ListStorageLensConfigurationsCLI"></a>

**Example List S3 Storage Lens configurations**  

```
aws s3control list-storage-lens-configurations --account-id=222222222222 --region=us-east-1 --next-token=abcdefghij1234
```

### Delete an S3 Storage Lens configuration<a name="S3DeleteStorageLensConfigurationCLI"></a>

**Example Delete an S3 Storage Lens configuration**  

```
aws s3control delete-storage-lens-configuration --account-id=222222222222 --region=us-east-1 --config-id=your-configuration-id
```

### Add tags to an S3 Storage Lens configuration<a name="S3PutStorageLensConfigurationTaggingCLI"></a>

**Example Add tags to an S3 Storage Lens configuration**  

```
aws s3control put-storage-lens-configuration-tagging --account-id=222222222222 --region=us-east-1 --config-id=your-configuration-id --tags=file://./tags.json
```

### Get tags for an S3 Storage Lens configuration<a name="S3GetStorageLensConfigurationTaggingCLI"></a>

**Example Get tags for an S3 Storage Lens configuration**  

```
aws s3control get-storage-lens-configuration-tagging --account-id=222222222222 --region=us-east-1 --config-id=your-configuration-id
```

### Delete tags for an S3 Storage Lens configuration<a name="S3DeleteStorageLensConfigurationTaggingCLI"></a>

**Example Delete tags for an S3 Storage Lens configuration**  

```
aws s3control delete-storage-lens-configuration-tagging --account-id=222222222222 --region=us-east-1 --config-id=your-configuration-id
```

## Using Amazon S3 Storage Lens with AWS Organizations examples using the AWS CLI<a name="S3LensOrganizationsCLI"></a>

Use Amazon S3 Storage Lens to collect storage metrics and usage data for all accounts that are part of your AWS Organizations hierarchy\. For more information, see [Using Amazon S3 Storage Lens with AWS Organizations](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage_lens_with_organizations.html)\. 

**Topics**
+ [Enable Organizations trusted access for S3 Storage Lens](#OrganizationsEnableTrustedAccessS3LensCLI)
+ [Disable Organizations trusted access for S3 Storage Lens](#OrganizationsDisableTrustedAccessS3LensCLI)
+ [Register Organizations delegated administrators for S3 Storage Lens](#OrganizationsRegisterDelegatedAdministratorS3LensCLI)
+ [Deregister Organizations delegated administrators for S3 Storage Lens](#OrganizationsDeregisterDelegatedAdministratorS3LensCLI)



### Enable Organizations trusted access for S3 Storage Lens<a name="OrganizationsEnableTrustedAccessS3LensCLI"></a>

**Example Enable Organizations trusted access for S3 Storage Lens**  

```
aws organizations enable-aws-service-access --service-principal storage-lens.s3.amazonaws.com
```

### Disable Organizations trusted access for S3 Storage Lens<a name="OrganizationsDisableTrustedAccessS3LensCLI"></a>

**Example Disable Organizations trusted access for S3 Storage Lens**  

```
aws organizations disable-aws-service-access --service-principal storage-lens.s3.amazonaws.com
```

### Register Organizations delegated administrators for S3 Storage Lens<a name="OrganizationsRegisterDelegatedAdministratorS3LensCLI"></a>

**Example Register Organizations delegated administrators for S3 Storage Lens**  
To use this example, replace `111122223333` with the appropriate AWS account ID\.  

```
aws organizations register-delegated-administrator --service-principal storage-lens.s3.amazonaws.com --account-id 111122223333
```

### Deregister Organizations delegated administrators for S3 Storage Lens<a name="OrganizationsDeregisterDelegatedAdministratorS3LensCLI"></a>

**Example Deregister Organizations delegated administrators for S3 Storage Lens**  
To use this example, replace `111122223333` with the appropriate AWS account ID\.  

```
aws organizations deregister-delegated-administrator --service-principal storage-lens.s3.amazonaws.com --account-id 111122223333
```