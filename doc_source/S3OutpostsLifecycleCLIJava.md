# Creating and managing a lifecycle configuration using the AWS CLI and SDK for Java<a name="S3OutpostsLifecycleCLIJava"></a>

Lifecycle rules for S3 on Outposts buckets are limited to object deletion\. You can use lifecycle rules to define when to initiate object deletion based on age or date\. You can create, enable, disable, or delete a lifecycle rule\.

**Note**  
The AWS account that creates the bucket owns it and is the only one that can create, enable, disable, or delete a lifecycle rule\.

To create and manage a lifecycle configuration for an S3 on Outposts using the AWS Command Line Interface \(AWS CLI\) and the AWS SDK for Java, see the following examples\.

**Topics**
+ [PUT a lifecycle configuration](#S3OutpostsPutBucketLifecycleConfiguration)
+ [GET the lifecycle configuration on an S3 on Outposts bucket](#S3OutpostsGetBucketLifecycleConfiguration)

## PUT a lifecycle configuration<a name="S3OutpostsPutBucketLifecycleConfiguration"></a>

------
#### [ AWS CLI ]

The following AWS CLI example puts a lifecycle configuration policy on an Outposts bucket\. This policy specifies that all objects that have the flagged prefix \(`myprefix`\) and tags expire after 10 days\.

1. Save the lifecycle configuration policy to a JSON file\. In this example, the file is named `lifecycle1.json`\.

   ```
   {
       "Rules": [
           {
               "ID": "id-1",
               "Filter": {
                   "And": {
                       "Prefix": "myprefix", 
                       "Tags": [
                           {
                               "Value": "mytagvalue1", 
                               "Key": "mytagkey1"
                           }, 
                           {
                               "Value": "mytagvalue2", 
                               "Key": "mytagkey2"
                           }
                       ]
                   }
               }, 
               "Status": "Enabled", 
               "Expiration": {
                   "Days": 10
               }
           }
       ]S3OutpostsPutBucketLifecycleConfigurationCLI
   }
   ```

1. Submit the JSON file as part of the put lifecycle configuration CLI command\.

   ```
   aws s3control put-bucket-lifecycle-configuration --account-id 123456789012 --bucket arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/bucket/example-outpost-bucket --lifecycle-configuration file://lifecycle1.json
   ```

------
#### [ SDK for Java ]

The following SDK for Java example puts a lifecycle configuration on an Outposts bucket\. This lifecycle configuration specifies that all objects that have the flagged prefix \(`myprefix`\) and tags expire after 10 days\.

```
import com.amazonaws.services.s3control.model.*;

public void putBucketLifecycleConfiguration(String bucketArn) {

    S3Tag tag1 = new S3Tag().withKey("mytagkey1").withValue("mytagvalue1");
    S3Tag tag2 = new S3Tag().withKey("mytagkey2").withValue("mytagvalue2");

    LifecycleRuleFilter lifecycleRuleFilter = new LifecycleRuleFilter()
            .withAnd(new LifecycleRuleAndOperator()
                    .withPrefix("myprefix")
                    .withTags(tag1, tag2));

    LifecycleExpiration lifecycleExpiration = new LifecycleExpiration()
            .withExpiredObjectDeleteMarker(false)
            .withDays(10);

    LifecycleRule lifecycleRule = new LifecycleRule()
            .withStatus("Enabled")
            .withFilter(lifecycleRuleFilter)
            .withExpiration(lifecycleExpiration)
            .withID("id-1");


    LifecycleConfiguration lifecycleConfiguration = new LifecycleConfiguration()
            .withRules(lifecycleRule);

    PutBucketLifecycleConfigurationRequest reqPutBucketLifecycle = new PutBucketLifecycleConfigurationRequest()
            .withAccountId(AccountId)
            .withBucket(bucketArn)
            .withLifecycleConfiguration(lifecycleConfiguration);

    PutBucketLifecycleConfigurationResult respPutBucketLifecycle = s3ControlClient.putBucketLifecycleConfiguration(reqPutBucketLifecycle);
    System.out.printf("PutBucketLifecycleConfiguration Response: %s%n", respPutBucketLifecycle.toString());


}
```

------

## GET the lifecycle configuration on an S3 on Outposts bucket<a name="S3OutpostsGetBucketLifecycleConfiguration"></a>

------
#### [ AWS CLI ]

The following AWS CLI example gets a lifecycle configuration on an Outposts bucket\.

```
aws s3control get-bucket-lifecycle-configuration --account-id 123456789012 --bucket arn:aws:s3-outposts:<your-region>:123456789012:outpost/op-01ac5d28a6a232904/bucket/example-outpost-bucket
```

------
#### [ SDK for Java ]

The following SDK for Java example gets a lifecycle configuration for an Outposts bucket\.

```
import com.amazonaws.services.s3control.model.*;

public void getBucketLifecycleConfiguration(String bucketArn) {

    GetBucketLifecycleConfigurationRequest reqGetBucketLifecycle = new GetBucketLifecycleConfigurationRequest()
            .withAccountId(AccountId)
            .withBucket(bucketArn);

    GetBucketLifecycleConfigurationResult respGetBucketLifecycle = s3ControlClient.getBucketLifecycleConfiguration(reqGetBucketLifecycle);
    System.out.printf("GetBucketLifecycleConfiguration Response: %s%n", respGetBucketLifecycle.toString());

}
```

------