# Creating a bucket<a name="create-bucket-overview"></a>

To upload your data to Amazon S3, you must first create an Amazon S3 bucket in one of the AWS Regions\. When you create a bucket, you must choose a bucket name and Region\. You can optionally choose other storage management options for the bucket\. After you create a bucket, you cannot change the bucket name or Region\. For information about naming buckets, see [Bucket naming rules](bucketnamingrules.md)\.

The AWS account that creates the bucket owns it\. You can upload any number of objects to the bucket\. By default, you can create up to 100 buckets in each of your AWS accounts\. If you need more buckets, you can increase your account bucket limit to a maximum of 1,000 buckets by submitting a service limit increase\. To learn how to submit a bucket limit increase, see [AWS service quotas](https://docs.aws.amazon.com/general/latest/gr/aws_service_limits.html) in the *AWS General Reference*\. You can store any number of objects in a bucket\. 

S3 Object Ownership is an Amazon S3 bucket\-level setting that you can use to disable access control lists \(ACLs\) and take ownership of every object in your bucket, simplifying access management for data stored in Amazon S3\. By default, when another AWS account uploads an object to your Amazon S3 bucket, that account \(the object writer\) owns the object, has access to it, and can grant other users access to it through ACLs\. When you create a bucket, you can apply the bucket owner enforced setting for Object Ownership to change this default behavior so that ACLs are disabled and you, as the bucket owner, automatically own every object in your bucket\. As a result, access control for your data is based on policies\. For more information, see [Controlling ownership of objects and disabling ACLs for your bucket](about-object-ownership.md)\.

**Important**  
Starting in April 2023, Amazon S3 will change the default settings for S3 Block Public Access and Object Ownership \(ACLs disabled\) for all new S3 buckets\. For new buckets created after this update, all S3 Block Public Access settings will be enabled, and S3 access control lists \(ACLs\) will be disabled\. These defaults are the recommended best practices for securing data in Amazon S3\. You can adjust these settings after creating your bucket\. For more information, see [ Default settings for new S3 buckets FAQ](create-bucket-faq.md) and [Heads\-Up: Amazon S3 Security Changes Are Coming in April of 2023](http://aws.amazon.com/blogs/aws/heads-up-amazon-s3-security-changes-are-coming-in-april-of-2023/) in the * AWS News Blog*\.

You can use the Amazon S3 console, Amazon S3 APIs, AWS CLI, or AWS SDKs to create a bucket\. For more information about the permissions required to create a bucket, see [CreateBucket](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CreateBucket.html) in the *Amazon Simple Storage Service API Reference*\.

## Using the S3 console<a name="create-bucket"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose **Create bucket**\.

   The **Create bucket** wizard opens\.

1. In **Bucket name**, enter a DNS\-compliant name for your bucket\.

   The bucket name must:
   + Be unique across all of Amazon S3\.
   + Be between 3 and 63 characters long\.
   + Not contain uppercase characters\.
   + Start with a lowercase letter or number\.

   After you create the bucket, you cannot change its name\. For information about naming buckets, see [Bucket naming rules](bucketnamingrules.md)\. 
**Important**  
Avoid including sensitive information, such as account number, in the bucket name\. The bucket name is visible in the URLs that point to the objects in the bucket\.

1. In **Region**, choose the AWS Region where you want the bucket to reside\. 

   Choose a Region close to you to minimize latency and costs and address regulatory requirements\. Objects stored in a Region never leave that Region unless you explicitly transfer them to another Region\. For a list of Amazon S3 AWS Regions, see [AWS service endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#s3_region) in the *Amazon Web Services General Reference*\.

1. Under **Object Ownership**, to disable or enable ACLs and control ownership of objects uploaded in your bucket, choose one of the following settings:

**ACLs disabled**
   + **Bucket owner enforced** – ACLs are disabled, and the bucket owner automatically owns and has full control over every object in the bucket\. ACLs no longer affect permissions to data in the S3 bucket\. The bucket uses policies to define access control\.

     To require that all new buckets are created with ACLs disabled by using IAM or AWS Organizations policies, see [Disabling ACLs for all new buckets \(bucket owner enforced\)](ensure-object-ownership.md#object-ownership-requiring-bucket-owner-enforced)\.

**ACLs enabled**
   + **Bucket owner preferred** – The bucket owner owns and has full control over new objects that other accounts write to the bucket with the `bucket-owner-full-control` canned ACL\. 

     If you apply the bucket owner preferred setting, to require all Amazon S3 uploads to include the `bucket-owner-full-control` canned ACL, you can [add a bucket policy](ensure-object-ownership.md#ensure-object-ownership-bucket-policy) that only allows object uploads that use this ACL\.
   + **Object writer** – The AWS account that uploads an object owns the object, has full control over it, and can grant other users access to it through ACLs\.
**Note**  
To apply the **Bucket owner enforced** setting or the **Bucket owner preferred** setting, you must have the following permission: `s3:CreateBucket` and `s3:PutBucketOwnershipControls`\.

1. In **Bucket settings for Block Public Access**, choose the Block Public Access settings that you want to apply to the bucket\. 

   We recommend that you keep all settings enabled unless you know that you need to turn off one or more of them for your use case, such as to host a public website\. Block Public Access settings that you enable for the bucket are also enabled for all access points that you create on the bucket\. For more information about blocking public access, see [Blocking public access to your Amazon S3 storage](access-control-block-public-access.md)\.

1. \(Optional\) Under **Bucket Versioning**, you can choose if you wish to keep variants of objects in your bucket\. For more information about versioning, see [Using versioning in S3 buckets](Versioning.md)\.

   To disable or enable versioning on your bucket, choose either **Disable** or **Enable**\.

1. \(Optional\) Under **Tags**, you can choose to add tags to your bucket\. Tags are key\-value pairs used to categorize storage\.

   To add a bucket tag, enter a **Key** and optionally a **Value** and choose **Add Tag**\.

1. \(Optional\) Under **Default encryption**, you can choose to configure your bucket to use server\-side encryption with either Amazon S3\-managed keys \(SSE\-S3\) or AWS KMS keys stored in AWS Key Management Service \(AWS KMS\) \(SSE\-KMS\)\. For more information, see [Setting default server\-side encryption behavior for Amazon S3 buckets](bucket-encryption.md)\.

   To disable or enable encryption, choose either **Disable** or **Enable**\.

1. \(Optional\) If you want to enable S3 Object Lock, do the following:

   1. Choose **Advanced settings**\.
**Important**  
You can only enable Object Lock for a bucket when you create it, and you cannot disable it later\. Enabling Object Lock also enables versioning for the bucket\. After enabling you must configure the Object Lock default retention and legal hold settings to protect new objects from being deleted or overwritten\.

   1. If you want to enable Object Lock, choose **Enable**, read the warning that appears, and acknowledge it\.

   For more information, see [Using S3 Object Lock](object-lock.md)\.
**Note**  
To create an Object Lock enabled bucket, you must have the following permissions: `s3:CreateBucket`, `s3:PutBucketVersioning` and `s3:PutBucketObjectLockConfiguration`\.

1. Choose **Create bucket**\.

## Using the AWS SDKs<a name="create-bucket-intro"></a>

When you use the AWS SDKs to create a bucket, you must create a client and then use the client to send a request to create a bucket\. As a best practice, you should create your client and bucket in the same AWS Region\. If you don't specify a Region when you create a client or a bucket, Amazon S3 uses the default Region US East \(N\. Virginia\)\. 

To create a client to access a dual\-stack endpoint, you must specify an AWS Region\. For more information, see [Dual\-stack endpoints](dual-stack-endpoints.md#dual-stack-endpoints-description)\. For a list of available AWS Regions, see [Regions and endpoints](https://docs.aws.amazon.com/general/latest/gr/s3.html) in the *AWS General Reference*\. 

When you create a client, the Region maps to the Region\-specific endpoint\. The client uses this endpoint to communicate with Amazon S3: `s3.<region>.amazonaws.com`\. If your Region launched after March 20, 2019, your client and bucket must be in the same Region\. However, you can use a client in the US East \(N\. Virginia\) Region to create a bucket in any Region that launched before March 20, 2019\. For more information, see [Legacy endpoints](VirtualHosting.md#s3-legacy-endpoints)\.

These AWS SDK code examples perform the following tasks:
+ **Create a client by explicitly specifying an AWS Region** — In the example, the client uses the `s3.us-west-2.amazonaws.com` endpoint to communicate with Amazon S3\. You can specify any AWS Region\. For a list of AWS Regions, see [Regions and endpoints](https://docs.aws.amazon.com/general/latest/gr/s3.html) in the *AWS General Reference*\. 
+ **Send a create bucket request by specifying only a bucket name** — The client sends a request to Amazon S3 to create the bucket in the Region where you created a client\. 
+ **Retrieve information about the location of the bucket** — Amazon S3 stores bucket location information in the *location* subresource that is associated with the bucket\.

------
#### [ Java ]

This example shows how to create an Amazon S3 bucket using the AWS SDK for Java\. For instructions on creating and testing a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\. 

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.CreateBucketRequest;
import com.amazonaws.services.s3.model.GetBucketLocationRequest;

import java.io.IOException;

public class CreateBucket2 {

    public static void main(String[] args) throws IOException {
        Regions clientRegion = Regions.DEFAULT_REGION;
        String bucketName = "*** Bucket name ***";

        try {
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .withCredentials(new ProfileCredentialsProvider())
                    .withRegion(clientRegion)
                    .build();

            if (!s3Client.doesBucketExistV2(bucketName)) {
                // Because the CreateBucketRequest object doesn't specify a region, the
                // bucket is created in the region specified in the client.
                s3Client.createBucket(new CreateBucketRequest(bucketName));

                // Verify that the bucket was created by retrieving it and checking its location.
                String bucketLocation = s3Client.getBucketLocation(new GetBucketLocationRequest(bucketName));
                System.out.println("Bucket location: " + bucketLocation);
            }
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

------
#### [ \.NET ]

For information about how to create and test a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\.

**Example**  

```
using Amazon;
using Amazon.S3;
using Amazon.S3.Model;
using Amazon.S3.Util;
using System;
using System.Threading.Tasks;

namespace Amazon.DocSamples.S3
{
    class CreateBucketTest
    {
        private const string bucketName = "*** bucket name ***";
        // Specify your bucket region (an example region is shown).
        private static readonly RegionEndpoint bucketRegion = RegionEndpoint.USWest2;
        private static IAmazonS3 s3Client;
        public static void Main()
        {
            s3Client = new AmazonS3Client(bucketRegion);
            CreateBucketAsync().Wait();
        }

        static async Task CreateBucketAsync()
        {
            try
            {
                if (!(await AmazonS3Util.DoesS3BucketExistAsync(s3Client, bucketName)))
                {
                    var putBucketRequest = new PutBucketRequest
                    {
                        BucketName = bucketName,
                        UseClientRegion = true
                    };

                    PutBucketResponse putBucketResponse = await s3Client.PutBucketAsync(putBucketRequest);
                }
                // Retrieve the bucket location.
                string bucketLocation = await FindBucketLocationAsync(s3Client);
            }
            catch (AmazonS3Exception e)
            {
                Console.WriteLine("Error encountered on server. Message:'{0}' when writing an object", e.Message);
            }
            catch (Exception e)
            {
                Console.WriteLine("Unknown encountered on server. Message:'{0}' when writing an object", e.Message);
            }
        }
        static async Task<string> FindBucketLocationAsync(IAmazonS3 client)
        {
            string bucketLocation;
            var request = new GetBucketLocationRequest()
            {
                BucketName = bucketName
            };
            GetBucketLocationResponse response = await client.GetBucketLocationAsync(request);
            bucketLocation = response.Location.ToString();
            return bucketLocation;
        }
    }
}
```

------
#### [ Ruby ]

For information about how to create and test a working sample, see [Using the AWS SDK for Ruby \- Version 3](UsingTheMPRubyAPI.md)\. 

**Example**  

```
require "aws-sdk-s3"

# Wraps Amazon S3 bucket actions.
class BucketCreateWrapper
  attr_reader :bucket

  # @param bucket [Aws::S3::Bucket] An Amazon S3 bucket initialized with a name. This is a client-side object until
  #                                 create is called.
  def initialize(bucket)
    @bucket = bucket
  end

  # Creates an Amazon S3 bucket in the specified AWS Region.
  #
  # @param region [String] The Region where the bucket is created.
  # @return [Boolean] True when the bucket is created; otherwise, false.
  def create?(region)
    @bucket.create(create_bucket_configuration: { location_constraint: region })
    true
  rescue Aws::Errors::ServiceError => e
    puts "Couldn't create bucket. Here's why: #{e.message}"
    false
  end

  # Gets the Region where the bucket is located.
  #
  # @return [String] The location of the bucket.
  def location
    if @bucket.nil?
      "None. You must create a bucket before you can get it's location!"
    else
      @bucket.client.get_bucket_location(bucket: @bucket.name).location_constraint
    end
  rescue Aws::Errors::ServiceError => e
    "Couldn't get the location of #{@bucket.name}. Here's why: #{e.message}"
  end
end

def run_demo
  region = "us-west-2"
  wrapper = BucketCreateWrapper.new(Aws::S3::Bucket.new("doc-example-bucket-#{Random.uuid}"))
  return unless wrapper.create?(region)

  puts "Created bucket #{wrapper.bucket.name}."
  puts "Your bucket's region is: #{wrapper.location}"
end

run_demo if $PROGRAM_NAME == __FILE__
```

------

## Using the AWS CLI<a name="creating-bucket-cli"></a>

You can also use the AWS Command Line Interface \(AWS CLI\) to create an S3 bucket\. For more information, see [create\-bucket](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/create-bucket.html) in the *AWS CLI Command Reference*\.

For information about the AWS CLI, see [What is the AWS Command Line Interface?](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html) in the *AWS Command Line Interface User Guide*\. 