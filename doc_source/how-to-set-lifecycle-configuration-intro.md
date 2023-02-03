# Setting lifecycle configuration on a bucket<a name="how-to-set-lifecycle-configuration-intro"></a>

This section explains how you can set a S3 Lifecycle configuration on a bucket using AWS SDKs, the AWS CLI, or the Amazon S3 console\. For information about S3 Lifecycle configuration, see [Managing your storage lifecycle](object-lifecycle-mgmt.md)\.

You can use lifecycle rules to define actions that you want Amazon S3 to take during an object's lifetime \(for example, transition objects to another storage class, archive them, or delete them after a specified period of time\)\.

Before you set a lifecycle configuration, note the following:

**Propagation delay**  
When you add an S3 Lifecycle configuration to a bucket, there is usually some lag before a new or updated Lifecycle configuration is fully propagated to all the Amazon S3 systems\. Expect a delay of a few minutes before the configuration fully takes effect\. This delay can also occur when you delete an S3 Lifecycle configuration\.

**Disabling or deleting Lifecycle rules**  
When you disable or delete Lifecycle rules, Amazon S3 stops scheduling new objects for deletion or transition after a small delay\. Any objects that were already scheduled are unscheduled and are not deleted or transitioned\.

**Existing and new objects**  
When you add a Lifecycle configuration to a bucket, the configuration rules apply to both existing objects and objects that you add later\. For example, if you add a Lifecycle configuration rule today with an expiration action that causes objects with a specific prefix to expire 30 days after creation, Amazon S3 will queue for removal any existing objects that are more than 30 days old\.

**Changes in billing**  
There may be a lag between when the Lifecycle configuration rules are satisfied and when the action triggered by satisfying the rule is taken\. However, changes in billing happen as soon as the Lifecycle configuration rule is satisfied, even if the action is not yet taken\.

For example, after the object expiration time, you are not charged for storage, even if the object is not deleted immediately\. Another example, as soon as the object transition time elapses, you are charged S3 Glacier Flexible Retrieval storage rates, even if the object is not immediately transitioned to the S3 Glacier Flexible Retrieval storage class\. Lifecycle transitions to the S3 Intelligent\-Tiering storage class are the exception\. Changes in billing do not happen until the object has transitioned into the S3 Intelligent\-Tiering storage class\. 

## Using the S3 console<a name="create-lifecycle"></a>

You can define a lifecycle rules for all objects or a subset of objects in the bucket by using a shared prefix \(objects names that begin with a common string\) or a tag\. Using a lifecycle rule you can define actions specific to current and non\-current object versions\. For more information, see the following:
+ [Managing your storage lifecycle](object-lifecycle-mgmt.md)
+ [Using versioning in S3 buckets](Versioning.md)

**To create a lifecycle rule**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want to create a lifecycle rule for\.

1. Choose the **Management** tab, and choose **Create lifecycle rule**\.

1. In **Lifecycle rule name**, enter a name for your rule\. 

   The name must be unique within the bucket\. 

1. Choose the scope of the lifecycle rule: 
   + To apply this lifecycle rule to *all objects with a specific prefix or tag*, choose **Limit the scope to specific prefixes or tags**\.
     + To limit the scope by prefix, in **Prefix**, enter the prefix\. 
     + To limit the scope by tag, choose **Add tag**, and enter the tag key and value\.

       For more information about object name prefixes, see [Creating object key names](object-keys.md)\. For more information about object tags, see [Categorizing your storage using tags](object-tagging.md)\. 
   + To apply this lifecycle rule to *all objects in the bucket*, choose **This rule applies to *all* objects in the bucket**, and choose **I acknowledge that this rule applies to all objects in the bucket**\.

1. To filter a rule by object size, you can check **Specify minimum object size**, **Specify maximum object size**, or both options\.
   + When you're specifying a **minimum object size** or **maximum object size**, the value must be larger than 0 bytes and up to 5TB\. You can specify this value in bytes, KB, MB, or GB\.
   + When you're specifying both, the maximum object size must be larger than the minimum object size\.

1. Under **Lifecycle rule actions**, choose the actions that you want your lifecycle rule to perform:
   + Transition *current* versions of objects between storage classes
   + Transition *previous* versions of objects between storage classes
   + Expire *current* versions of objects
   + Permanently delete *previous* versions of objects
   + Delete expired delete markers or incomplete multipart uploads 

   Depending on the actions that you choose, different options appear\.

1. To transition *current* versions of objects between storage classes, under **Transition current versions of objects between storage classes**:

   1. In **Storage class transitions**, choose the storage class to transition to:
      + Standard\-IA
      + Intelligent\-Tiering
      + One Zone\-IA
      + S3 Glacier Flexible Retrieval
      + Glacier Deep Archive

   1. In **Days after object creation**, enter the number of days after creation to transition the object\.

   For more information about storage classes, see [Using Amazon S3 storage classes](storage-class-intro.md)\. You can define transitions for current or previous object versions or for both current and previous versions\. Versioning enables you to keep multiple versions of an object in one bucket\. For more information about versioning, see [Using the S3 console](manage-versioning-examples.md#enable-versioning)\.
**Important**  
When you choose the S3 Glacier Flexible Retrieval or Glacier Deep Archive storage class, your objects remain in Amazon S3\. You cannot access them directly through the separate Amazon S3 Glacier service\. For more information, see [Transitioning objects using Amazon S3 Lifecycle](lifecycle-transition-general-considerations.md)\. 

1. To transition *non\-current* versions of objects between storage classes, under **Transition non\-current versions of objects between storage classes**:

   1. In **Storage class transitions**, choose the storage class to transition to:
      + Standard\-IA
      + Intelligent\-Tiering
      + One Zone\-IA
      + S3 Glacier Flexible Retrieval
      + Glacier Deep Archive

   1. In **Days after object becomes non\-current**, enter the number of days after creation to transition the object\.

1. To expire *current* versions of objects, under **Expire current versions of objects**, in **Number of days after object creation**, enter the number of days\.
**Important**  
In a non\-versioned bucket the expiration action results in Amazon S3 permanently removing the object\. For more information about lifecycle actions, see [Elements to describe lifecycle actions](intro-lifecycle-rules.md#intro-lifecycle-rules-actions)\.

1. To permanently delete previous versions of objects, under **Permanently delete noncurrent versions of objects**, in **Days after objects become noncurrent**, enter the number of days\. You can optionally specify the number of newer versions to retain by entering a value under **Number of newer versions to retain**\.

1. Under **Delete expired delete markers or incomplete multipart uploads**, choose **Delete expired object delete markers** and **Delete incomplete multipart uploads**\. Then, enter the number of days after the multipart upload initiation that you want to end and clean up incomplete multipart uploads\.

   For more information about multipart uploads, see [Uploading and copying objects using multipart upload](mpuoverview.md)\.

1. Choose **Create rule**\.

   If the rule does not contain any errors, Amazon S3 enables it, and you can see it on the **Management** tab under **Lifecycle rules**\.

For information about CloudFormation templates and examples, see [Working with AWS CloudFormation templates](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-guide.html) and [AWS::S3::Bucket](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-s3-bucket.html#aws-resource-s3-bucket--examples) in the *AWS CloudFormation User Guide*\.

## Using the AWS CLI<a name="set-lifecycle-cli"></a>

You can use the following AWS CLI commands to manage S3 Lifecycle configurations:
+ `put-bucket-lifecycle-configuration`
+ `get-bucket-lifecycle-configuration`
+ `delete-bucket-lifecycle`

For instructions on setting up the AWS CLI, see [Developing with Amazon S3 using the AWS CLI](setup-aws-cli.md)\.

The Amazon S3 Lifecycle configuration is an XML file\. But when using the AWS CLI, you cannot specify the XML\. You must specify the JSON instead\. The following are example XML Lifecycle configurations and equivalent JSON that you can specify in an AWS CLIcommand\.

Consider the following example S3 Lifecycle configuration\.

**Example 1**  

```
{
    "Rules": [
        {
            "Filter": {
                "Prefix": "documents/"
            },
            "Status": "Enabled",
            "Transitions": [
                {
                    "Days": 365,
                    "StorageClass": "GLACIER"
                }
            ],
            "Expiration": {
                "Days": 3650
            },
            "ID": "ExampleRule"
        }
    ]
        }
```

```
<LifecycleConfiguration>
    <Rule>
        <ID>ExampleRule</ID>
        <Filter>
           <Prefix>documents/</Prefix>
        </Filter>
        <Status>Enabled</Status>
        <Transition>        
           <Days>365</Days>        
           <StorageClass>GLACIER</StorageClass>
        </Transition>    
        <Expiration>
             <Days>3650</Days>
        </Expiration>
    </Rule>
</LifecycleConfiguration>
```

**Example 2**  

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
                "Days": 1
            }
        }
    ]
}
```

```
<LifecycleConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
    <Rule>
        <ID>id-1</ID>
        <Expiration>
            <Days>1</Days>
        </Expiration>
        <Filter>
            <And>
                <Prefix>myprefix</Prefix>
                <Tag>
                    <Key>mytagkey1</Key>
                    <Value>mytagvalue1</Value>
                </Tag>
                <Tag>
                    <Key>mytagkey2</Key>
                    <Value>mytagvalue2</Value>
                </Tag>
            </And>
        </Filter>
        <Status>Enabled</Status>    
    </Rule>
</LifecycleConfiguration>
```

You can test the `put-bucket-lifecycle-configuration` as follows\.

**To test the configuration**

1. Save the JSON Lifecycle configuration in a file \(`lifecycle.json`\)\. 

1. Run the following AWS CLI command to set the Lifecycle configuration on your bucket\.

   ```
   $ aws s3api put-bucket-lifecycle-configuration  \
   --bucket bucketname  \
   --lifecycle-configuration file://lifecycle.json
   ```

1. To verify, retrieve the S3 Lifecycle configuration using the `get-bucket-lifecycle-configuration` AWS CLI command as follows\.

   ```
   $ aws s3api get-bucket-lifecycle-configuration  \
   --bucket bucketname
   ```

1. To delete the S3 Lifecycle configuration use the `delete-bucket-lifecycle` AWS CLI command as follows\.

   ```
   aws s3api delete-bucket-lifecycle \
   --bucket bucketname
   ```

## Using the AWS SDKs<a name="manage-lifecycle-using-sdk"></a>

------
#### [ Java ]

You can use the AWS SDK for Java to manage the S3 Lifecycle configuration of a bucket\. For more information about managing S3 Lifecycle configuration, see [Managing your storage lifecycle](object-lifecycle-mgmt.md)\.

**Note**  
When you add S3 Lifecycle configuration to a bucket, Amazon S3 replaces the bucket's current Lifecycle configuration, if there is one\. To update a configuration, you retrieve it, make the desired changes, and then add the revised configuration to the bucket\.

The following example shows how to use the AWS SDK for Java to add, update, and delete the Lifecycle configuration of a bucket\. The example does the following:
+ Adds a Lifecycle configuration to a bucket\. 
+ Retrieves the Lifecycle configuration and updates it by adding another rule\. 
+ Adds the modified Lifecycle configuration to the bucket\. Amazon S3 replaces the existing configuration\. 
+ Retrieves the configuration again and verifies that it has the right number of rules by the printing number of rules\.
+ Deletes the Lifecycle configuration and verifies that it has been deleted by attempting to retrieve it again\.

 For instructions on creating and testing a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\. 

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.BucketLifecycleConfiguration;
import com.amazonaws.services.s3.model.BucketLifecycleConfiguration.Transition;
import com.amazonaws.services.s3.model.StorageClass;
import com.amazonaws.services.s3.model.Tag;
import com.amazonaws.services.s3.model.lifecycle.LifecycleAndOperator;
import com.amazonaws.services.s3.model.lifecycle.LifecycleFilter;
import com.amazonaws.services.s3.model.lifecycle.LifecyclePrefixPredicate;
import com.amazonaws.services.s3.model.lifecycle.LifecycleTagPredicate;

import java.io.IOException;
import java.util.Arrays;

public class LifecycleConfiguration {

    public static void main(String[] args) throws IOException {
        Regions clientRegion = Regions.DEFAULT_REGION;
        String bucketName = "*** Bucket name ***";

        // Create a rule to archive objects with the "glacierobjects/" prefix to Glacier immediately.
        BucketLifecycleConfiguration.Rule rule1 = new BucketLifecycleConfiguration.Rule()
                .withId("Archive immediately rule")
                .withFilter(new LifecycleFilter(new LifecyclePrefixPredicate("glacierobjects/")))
                .addTransition(new Transition().withDays(0).withStorageClass(StorageClass.Glacier))
                .withStatus(BucketLifecycleConfiguration.ENABLED);

        // Create a rule to transition objects to the Standard-Infrequent Access storage class 
        // after 30 days, then to Glacier after 365 days. Amazon S3 will delete the objects after 3650 days.
        // The rule applies to all objects with the tag "archive" set to "true". 
        BucketLifecycleConfiguration.Rule rule2 = new BucketLifecycleConfiguration.Rule()
                .withId("Archive and then delete rule")
                .withFilter(new LifecycleFilter(new LifecycleTagPredicate(new Tag("archive", "true"))))
                .addTransition(new Transition().withDays(30).withStorageClass(StorageClass.StandardInfrequentAccess))
                .addTransition(new Transition().withDays(365).withStorageClass(StorageClass.Glacier))
                .withExpirationInDays(3650)
                .withStatus(BucketLifecycleConfiguration.ENABLED);

        // Add the rules to a new BucketLifecycleConfiguration.
        BucketLifecycleConfiguration configuration = new BucketLifecycleConfiguration()
                .withRules(Arrays.asList(rule1, rule2));

        try {
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .withCredentials(new ProfileCredentialsProvider())
                    .withRegion(clientRegion)
                    .build();

            // Save the configuration.
            s3Client.setBucketLifecycleConfiguration(bucketName, configuration);

            // Retrieve the configuration.
            configuration = s3Client.getBucketLifecycleConfiguration(bucketName);

            // Add a new rule with both a prefix predicate and a tag predicate.
            configuration.getRules().add(new BucketLifecycleConfiguration.Rule().withId("NewRule")
                    .withFilter(new LifecycleFilter(new LifecycleAndOperator(
                            Arrays.asList(new LifecyclePrefixPredicate("YearlyDocuments/"),
                                    new LifecycleTagPredicate(new Tag("expire_after", "ten_years"))))))
                    .withExpirationInDays(3650)
                    .withStatus(BucketLifecycleConfiguration.ENABLED));

            // Save the configuration.
            s3Client.setBucketLifecycleConfiguration(bucketName, configuration);

            // Retrieve the configuration.
            configuration = s3Client.getBucketLifecycleConfiguration(bucketName);

            // Verify that the configuration now has three rules.
            configuration = s3Client.getBucketLifecycleConfiguration(bucketName);
            System.out.println("Expected # of rules = 3; found: " + configuration.getRules().size());

            // Delete the configuration.
            s3Client.deleteBucketLifecycleConfiguration(bucketName);

            // Verify that the configuration has been deleted by attempting to retrieve it.
            configuration = s3Client.getBucketLifecycleConfiguration(bucketName);
            String s = (configuration == null) ? "No configuration found." : "Configuration found.";
            System.out.println(s);
        } catch (AmazonServiceException e) {
            // The call was transmitted successfully, but Amazon S3 couldn't process 
            // it, so it returned an error response.
            e.printStackTrace();
        } catch (SdkClientException e) {
            // Amazon S3 couldn't be contacted for a response, or the client
            // couldn't parse the response from Amazon S3.
            e.printStackTrace();
        }
    }
}
```

------
#### [ \.NET ]

You can use the AWS SDK for \.NET to manage the S3 Lifecycle configuration on a bucket\. For more information about managing Lifecycle configuration, see [Managing your storage lifecycle](object-lifecycle-mgmt.md)\. 

**Note**  
When you add a Lifecycle configuration, Amazon S3 replaces the existing configuration on the specified bucket\. To update a configuration, you must first retrieve the Lifecycle configuration, make the changes, and then add the revised Lifecycle configuration to the bucket\.

The following example shows how to use the AWS SDK for \.NET to add, update, and delete a bucket's Lifecycle configuration\. The code example does the following:
+ Adds a Lifecycle configuration to a bucket\. 
+ Retrieves the Lifecycle configuration and updates it by adding another rule\. 
+ Adds the modified Lifecycle configuration to the bucket\. Amazon S3 replaces the existing Lifecycle configuration\.
+ Retrieves the configuration again and verifies it by printing the number of rules in the configuration\.
+ Deletes the Lifecycle configuration and verifies the deletion\.

For instructions on how to create and test a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\.

```
using Amazon;
using Amazon.S3;
using Amazon.S3.Model;
using System;
using System.Collections.Generic;
using System.Threading.Tasks;

namespace Amazon.DocSamples.S3
{
    class LifecycleTest
    {
        private const string bucketName = "*** bucket name ***";
        // Specify your bucket region (an example region is shown).
        private static readonly RegionEndpoint bucketRegion = RegionEndpoint.USWest2;
        private static IAmazonS3 client;
        public static void Main()
        {
            client = new AmazonS3Client(bucketRegion);
            AddUpdateDeleteLifecycleConfigAsync().Wait();
        }

        private static async Task AddUpdateDeleteLifecycleConfigAsync()
        {
            try
            {
                var lifeCycleConfiguration = new LifecycleConfiguration()
                {
                    Rules = new List<LifecycleRule>
                        {
                            new LifecycleRule
                            {
                                 Id = "Archive immediately rule",
                                 Filter = new LifecycleFilter()
                                 {
                                     LifecycleFilterPredicate = new LifecyclePrefixPredicate()
                                     {
                                         Prefix = "glacierobjects/"
                                     }
                                 },
                                 Status = LifecycleRuleStatus.Enabled,
                                 Transitions = new List<LifecycleTransition>
                                 {
                                      new LifecycleTransition
                                      {
                                           Days = 0,
                                           StorageClass = S3StorageClass.Glacier
                                      }
                                  },
                            },
                            new LifecycleRule
                            {
                                 Id = "Archive and then delete rule",
                                  Filter = new LifecycleFilter()
                                 {
                                     LifecycleFilterPredicate = new LifecyclePrefixPredicate()
                                     {
                                         Prefix = "projectdocs/"
                                     }
                                 },
                                 Status = LifecycleRuleStatus.Enabled,
                                 Transitions = new List<LifecycleTransition>
                                 {
                                      new LifecycleTransition
                                      {
                                           Days = 30,
                                           StorageClass = S3StorageClass.StandardInfrequentAccess
                                      },
                                      new LifecycleTransition
                                      {
                                        Days = 365,
                                        StorageClass = S3StorageClass.Glacier
                                      }
                                 },
                                 Expiration = new LifecycleRuleExpiration()
                                 {
                                       Days = 3650
                                 }
                            }
                        }
                };

                // Add the configuration to the bucket. 
                await AddExampleLifecycleConfigAsync(client, lifeCycleConfiguration);

                // Retrieve an existing configuration. 
                lifeCycleConfiguration = await RetrieveLifecycleConfigAsync(client);

                // Add a new rule.
                lifeCycleConfiguration.Rules.Add(new LifecycleRule
                {
                    Id = "NewRule",
                    Filter = new LifecycleFilter()
                    {
                        LifecycleFilterPredicate = new LifecyclePrefixPredicate()
                        {
                            Prefix = "YearlyDocuments/"
                        }
                    },
                    Expiration = new LifecycleRuleExpiration()
                    {
                        Days = 3650
                    }
                });

                // Add the configuration to the bucket. 
                await AddExampleLifecycleConfigAsync(client, lifeCycleConfiguration);

                // Verify that there are now three rules.
                lifeCycleConfiguration = await RetrieveLifecycleConfigAsync(client);
                Console.WriteLine("Expected # of rulest=3; found:{0}", lifeCycleConfiguration.Rules.Count);

                // Delete the configuration.
                await RemoveLifecycleConfigAsync(client);

                // Retrieve a nonexistent configuration.
                lifeCycleConfiguration = await RetrieveLifecycleConfigAsync(client);

            }
            catch (AmazonS3Exception e)
            {
                Console.WriteLine("Error encountered ***. Message:'{0}' when writing an object", e.Message);
            }
            catch (Exception e)
            {
                Console.WriteLine("Unknown encountered on server. Message:'{0}' when writing an object", e.Message);
            }
        }

        static async Task AddExampleLifecycleConfigAsync(IAmazonS3 client, LifecycleConfiguration configuration)
        {

            PutLifecycleConfigurationRequest request = new PutLifecycleConfigurationRequest
            {
                BucketName = bucketName,
                Configuration = configuration
            };
            var response = await client.PutLifecycleConfigurationAsync(request);
        }

        static async Task<LifecycleConfiguration> RetrieveLifecycleConfigAsync(IAmazonS3 client)
        {
            GetLifecycleConfigurationRequest request = new GetLifecycleConfigurationRequest
            {
                BucketName = bucketName
            };
            var response = await client.GetLifecycleConfigurationAsync(request);
            var configuration = response.Configuration;
            return configuration;
        }

        static async Task RemoveLifecycleConfigAsync(IAmazonS3 client)
        {
            DeleteLifecycleConfigurationRequest request = new DeleteLifecycleConfigurationRequest
            {
                BucketName = bucketName
            };
            await client.DeleteLifecycleConfigurationAsync(request);
        }
    }
}
```

------
#### [ Ruby ]

You can use the AWS SDK for Ruby to manage S3 Lifecycle configuration on a bucket by using the class [ AWS::S3::BucketLifecycleConfiguration](https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/S3/BucketLifecycle.html)\. For more information about using the AWS SDK for Ruby with Amazon S3, see [Using the AWS SDK for Ruby \- Version 3](UsingTheMPRubyAPI.md)\. For more information about managing lifecycle configuration, see [Managing your storage lifecycle](object-lifecycle-mgmt.md)\. 

------

## Using the REST API<a name="manage-lifecycle-using-rest"></a>

The following sections in the *Amazon Simple Storage Service API Reference* describe the REST API related to the S3 Lifecycle configuration\. 
+ [PUT Bucket lifecycle](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTlifecycle.html)
+ [GET Bucket lifecycle](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETlifecycle.html)
+ [DELETE Bucket lifecycle](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketDELETElifecycle.html)