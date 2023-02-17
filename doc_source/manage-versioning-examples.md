# Enabling versioning on buckets<a name="manage-versioning-examples"></a>

You can use S3 Versioning to keep multiple versions of an object in one bucket\. This section provides examples of how to enable versioning on a bucket using the console, REST API, AWS SDKs, and AWS Command Line Interface \(AWS CLI\)\. 

**Note**  
 If you enable versioning on a bucket for the first time, it might take a short amount of time for the change to be fully propagated\. We recommend that you wait for 15 minutes after enabling versioning before issuing write operations \(PUT or DELETE\) on objects in the bucket\. 

For more information about S3 Versioning, see [Using versioning in S3 buckets](Versioning.md)\. For information about working with objects that are in versioning\-enabled buckets, see [Working with objects in a versioning\-enabled bucket](manage-objects-versioned-bucket.md)\.

To learn more about how to use S3 Versioning to protect data, see [Tutorial: Protecting data on Amazon S3 against accidental deletion or application bugs using S3 Versioning, S3 Object Lock, and S3 Replication](http://aws.amazon.com/getting-started/hands-on/protect-data-on-amazon-s3/?ref=docs_gateway/amazons3/manage-versioning-examples.html)\.

Each S3 bucket that you create has a *versioning* subresource associated with it\. \(For more information, see [Bucket configuration options](UsingBucket.md#bucket-config-options-intro)\.\) By default, your bucket is *unversioned*, and the versioning subresource stores the empty versioning configuration, as follows\.

```
<VersioningConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/"> 
</VersioningConfiguration>
```

To enable versioning, you can send a request to Amazon S3 with a versioning configuration that includes a status\. 

```
<VersioningConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/"> 
  <Status>Enabled</Status> 
</VersioningConfiguration>
```

To suspend versioning, you set the status value to `Suspended`\.

The bucket owner and all authorized users can enable versioning\. The bucket owner is the AWS account that created the bucket \(the root account\)\. For more information about permissions, see [Identity and access management in Amazon S3](s3-access-control.md)\.

The following sections provide more detail about enabling S3 Versioning using the console, AWS CLI, and the AWS SDKs\.

## Using the S3 console<a name="enable-versioning"></a>

Follow these steps to use the AWS Management Console to enable versioning on an S3 bucket\.

**To enable or disable versioning on an S3 bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want to enable versioning for\.

1. Choose **Properties**\.

1. Under **Bucket Versioning**, choose **Edit**\.

1. Choose **Suspend** or **Enable**, and then choose **Save changes**\.

**Note**  
You can use AWS multi\-factor authentication \(MFA\) with versioning\. When you use MFA with versioning, you must provide your AWS account’s access keys and a valid code from the account’s MFA device to permanently delete an object version or suspend or reactivate versioning\.   
To use MFA with versioning, you enable `MFA Delete`\. However, you can't enable `MFA Delete` using the AWS Management Console\. You must use the AWS Command Line Interface \(AWS CLI\) or the API\. For more information, see [Configuring MFA delete](MultiFactorAuthenticationDelete.md)\.

## Using the AWS CLI<a name="manage-versioning-examples-cli"></a>

The following example enables versioning on an S3 bucket\. 

```
aws s3api put-bucket-versioning --bucket DOC-EXAMPLE-BUCKET1 --versioning-configuration Status=Enabled
```

The following example enables S3 Versioning and multi\-factor authentication \(MFA\) delete on a bucket\.

```
aws s3api put-bucket-versioning --bucket DOC-EXAMPLE-BUCKET1 --versioning-configuration Status=Enabled,MFADelete=Enabled --mfa "SERIAL 123456"
```

**Note**  
Using MFA delete requires an approved physical or virtual authentication device\. For more information about using MFA delete in Amazon S3, see [Configuring MFA delete](MultiFactorAuthenticationDelete.md)\.

For more information about enabling versioning using the AWS CLI, see [put\-bucket\-versioning](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/put-bucket-versioning.html) in the *AWS CLI Command Reference*\.

## Using the AWS SDKs<a name="manage-versioning-examples-sdk"></a>

The following examples enable versioning on a bucket and then retrieve versioning status using the AWS SDK for Java and the AWS SDK for \.NET\. For information about using other AWS SDKs, see the [AWS Developer Center](https://aws.amazon.com/code/)\.

------
#### [ \.NET ]

For information about how to create and test a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\. 

```
using System;
using Amazon.S3;
using Amazon.S3.Model;

namespace s3.amazon.com.docsamples
{
    class BucketVersioningConfiguration
    {
        static string bucketName = "*** bucket name ***";

        public static void Main(string[] args)
        {
            using (var client = new AmazonS3Client(Amazon.RegionEndpoint.USEast1))
            {
                try
                {
                    EnableVersioningOnBucket(client);
                    string bucketVersioningStatus = RetrieveBucketVersioningConfiguration(client);
                }
                catch (AmazonS3Exception amazonS3Exception)
                {
                    if (amazonS3Exception.ErrorCode != null &&
                        (amazonS3Exception.ErrorCode.Equals("InvalidAccessKeyId")
                        ||
                        amazonS3Exception.ErrorCode.Equals("InvalidSecurity")))
                    {
                        Console.WriteLine("Check the provided AWS Credentials.");
                        Console.WriteLine(
                        "To sign up for service, go to http://aws.amazon.com/s3");
                    }
                    else
                    {
                        Console.WriteLine(
                         "Error occurred. Message:'{0}' when listing objects",
                         amazonS3Exception.Message);
                    }
                }
            }

            Console.WriteLine("Press any key to continue...");
            Console.ReadKey();
        }

        static void EnableVersioningOnBucket(IAmazonS3 client)
        {

                PutBucketVersioningRequest request = new PutBucketVersioningRequest
                {
                    BucketName = bucketName,
                    VersioningConfig = new S3BucketVersioningConfig 
                    {
                        Status = VersionStatus.Enabled
                    }
                };

                PutBucketVersioningResponse response = client.PutBucketVersioning(request);
        }


        static string RetrieveBucketVersioningConfiguration(IAmazonS3 client)
        {
                GetBucketVersioningRequest request = new GetBucketVersioningRequest
                {
                    BucketName = bucketName
                };
 
                GetBucketVersioningResponse response = client.GetBucketVersioning(request);
                return response.VersioningConfig.Status;
            }
    }
}
```

------
#### [ Java ]

For instructions on how to create and test a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\. 

```
import java.io.IOException;

import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Region;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3Client;
import com.amazonaws.services.s3.model.AmazonS3Exception;
import com.amazonaws.services.s3.model.BucketVersioningConfiguration;
import com.amazonaws.services.s3.model.SetBucketVersioningConfigurationRequest;

public class BucketVersioningConfigurationExample {
    public static String bucketName = "*** bucket name ***"; 
    public static AmazonS3Client s3Client;

    public static void main(String[] args) throws IOException {
        s3Client = new AmazonS3Client(new ProfileCredentialsProvider());
        s3Client.setRegion(Region.getRegion(Regions.US_EAST_1));
        try {

            // 1. Enable versioning on the bucket.
        	BucketVersioningConfiguration configuration = 
        			new BucketVersioningConfiguration().withStatus("Enabled");
            
			SetBucketVersioningConfigurationRequest setBucketVersioningConfigurationRequest = 
					new SetBucketVersioningConfigurationRequest(bucketName,configuration);
			
			s3Client.setBucketVersioningConfiguration(setBucketVersioningConfigurationRequest);
			
			// 2. Get bucket versioning configuration information.
			BucketVersioningConfiguration conf = s3Client.getBucketVersioningConfiguration(bucketName);
			 System.out.println("bucket versioning configuration status:    " + conf.getStatus());

        } catch (AmazonS3Exception amazonS3Exception) {
            System.out.format("An Amazon S3 error occurred. Exception: %s", amazonS3Exception.toString());
        } catch (Exception ex) {
            System.out.format("Exception: %s", ex.toString());
        }        
    }
}
```

------
#### [ Python ]

For instructions on how to create and test a working sample, see [Using the AWS SDK for Python \(Boto\)](UsingTheBotoAPI.md)\. 

The following Python code example creates an Amazon S3 bucket, enables it for versioning, and configures a lifecycle that expires noncurrent object versions after 7 days\.

```
def create_versioned_bucket(bucket_name, prefix):
    """
    Creates an Amazon S3 bucket, enables it for versioning, and configures a lifecycle
    that expires noncurrent object versions after 7 days.

    Adding a lifecycle configuration to a versioned bucket is a best practice.
    It helps prevent objects in the bucket from accumulating a large number of
    noncurrent versions, which can slow down request performance.

    Usage is shown in the usage_demo_single_object function at the end of this module.

    :param bucket_name: The name of the bucket to create.
    :param prefix: Identifies which objects are automatically expired under the
                   configured lifecycle rules.
    :return: The newly created bucket.
    """
    try:
        bucket = s3.create_bucket(
            Bucket=bucket_name,
            CreateBucketConfiguration={
                'LocationConstraint': s3.meta.client.meta.region_name
            }
        )
        logger.info("Created bucket %s.", bucket.name)
    except ClientError as error:
        if error.response['Error']['Code'] == 'BucketAlreadyOwnedByYou':
            logger.warning("Bucket %s already exists! Using it.", bucket_name)
            bucket = s3.Bucket(bucket_name)
        else:
            logger.exception("Couldn't create bucket %s.", bucket_name)
            raise

    try:
        bucket.Versioning().enable()
        logger.info("Enabled versioning on bucket %s.", bucket.name)
    except ClientError:
        logger.exception("Couldn't enable versioning on bucket %s.", bucket.name)
        raise

    try:
        expiration = 7
        bucket.LifecycleConfiguration().put(
            LifecycleConfiguration={
                'Rules': [{
                    'Status': 'Enabled',
                    'Prefix': prefix,
                    'NoncurrentVersionExpiration': {'NoncurrentDays': expiration}
                }]
            }
        )
        logger.info("Configured lifecycle to expire noncurrent versions after %s days "
                    "on bucket %s.", expiration, bucket.name)
    except ClientError as error:
        logger.warning("Couldn't configure lifecycle on bucket %s because %s. "
                       "Continuing anyway.", bucket.name, error)

    return bucket
```

------