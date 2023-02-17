# Making requests using federated user temporary credentials<a name="AuthUsingTempFederationToken"></a>

You can request temporary security credentials and provide them to your federated users or applications who need to access your AWS resources\. This section provides examples of how you can use the AWS SDK to obtain temporary security credentials for your federated users or applications and send authenticated requests to Amazon S3 using those credentials\. For a list of available AWS SDKs, see [Sample Code and Libraries](https://aws.amazon.com/code/)\. 

**Note**  
Both the AWS account and an IAM user can request temporary security credentials for federated users\. However, for added security, only an IAM user with the necessary permissions should request these temporary credentials to ensure that the federated user gets at most the permissions of the requesting IAM user\. In some applications, you might find it suitable to create an IAM user with specific permissions for the sole purpose of granting temporary security credentials to your federated users and applications\.

------
#### [ Java ]

You can provide temporary security credentials for your federated users and applications so that they can send authenticated requests to access your AWS resources\. When requesting these temporary credentials, you must provide a user name and an IAM policy that describes the resource permissions that you want to grant\. By default, the session duration is one hour\. You can explicitly set a different duration value when requesting the temporary security credentials for federated users and applications\.

**Note**  
For added security when requesting temporary security credentials for federated users and applications, we recommend that you use a dedicated IAM user with only the necessary access permissions\. The temporary user you create can never get more permissions than the IAM user who requested the temporary security credentials\. For more information, see [AWS Identity and Access Management FAQs ](https://aws.amazon.com/iam/faqs/#What_are_the_best_practices_for_using_temporary_security_credentials)\.

To provide security credentials and send authenticated request to access resources, do the following:
+ Create an instance of the `AWSSecurityTokenServiceClient` class\. For information about providing credentials, see [Using the AWS SDK for Java](UsingTheMPJavaAPI.md)\.
+ Start a session by calling the `getFederationToken()` method of the Security Token Service \(STS\) client\. Provide session information, including the user name and an IAM policy, that you want to attach to the temporary credentials\. You can provide an optional session duration\. This method returns your temporary security credentials\.
+ Package the temporary security credentials in an instance of the `BasicSessionCredentials` object\. You use this object to provide the temporary security credentials to your Amazon S3 client\.
+ Create an instance of the `AmazonS3Client` class using the temporary security credentials\. You send requests to Amazon S3 using this client\. If you send requests using expired credentials, Amazon S3 returns an error\. 

**Example**  
The example lists keys in the specified S3 bucket\. In the example, you obtain temporary security credentials for a two\-hour session for your federated user and use the credentials to send authenticated requests to Amazon S3\. To run the example, you need to create an IAM user with an attached policy that allows the user to request temporary security credentials and list your AWS resources\. The following policy accomplishes this:  

```
 1. {
 2.   "Statement":[{
 3.       "Action":["s3:ListBucket",
 4.         "sts:GetFederationToken*"
 5.       ],
 6.       "Effect":"Allow",
 7.       "Resource":"*"
 8.     }
 9.   ]
10. }
```
For more information about how to create an IAM user, see [Creating Your First IAM user and Administrators Group](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html) in the *IAM User Guide*\.   
After creating an IAM user and attaching the preceding policy, you can run the following example\. For instructions on creating and testing a working sample, see [Testing the Amazon S3 Java Code Examples](UsingTheMPJavaAPI.md#TestingJavaSamples)\.  

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.auth.AWSStaticCredentialsProvider;
import com.amazonaws.auth.BasicSessionCredentials;
import com.amazonaws.auth.policy.Policy;
import com.amazonaws.auth.policy.Resource;
import com.amazonaws.auth.policy.Statement;
import com.amazonaws.auth.policy.Statement.Effect;
import com.amazonaws.auth.policy.actions.S3Actions;
import com.amazonaws.auth.profile.ProfileCredentialsProvider;
import com.amazonaws.regions.Regions;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.ObjectListing;
import com.amazonaws.services.securitytoken.AWSSecurityTokenService;
import com.amazonaws.services.securitytoken.AWSSecurityTokenServiceClientBuilder;
import com.amazonaws.services.securitytoken.model.Credentials;
import com.amazonaws.services.securitytoken.model.GetFederationTokenRequest;
import com.amazonaws.services.securitytoken.model.GetFederationTokenResult;

import java.io.IOException;

public class MakingRequestsWithFederatedTempCredentials {

    public static void main(String[] args) throws IOException {
        Regions clientRegion = Regions.DEFAULT_REGION;
        String bucketName = "*** Specify bucket name ***";
        String federatedUser = "*** Federated user name ***";
        String resourceARN = "arn:aws:s3:::" + bucketName;

        try {
            AWSSecurityTokenService stsClient = AWSSecurityTokenServiceClientBuilder
                    .standard()
                    .withCredentials(new ProfileCredentialsProvider())
                    .withRegion(clientRegion)
                    .build();

            GetFederationTokenRequest getFederationTokenRequest = new GetFederationTokenRequest();
            getFederationTokenRequest.setDurationSeconds(7200);
            getFederationTokenRequest.setName(federatedUser);

            // Define the policy and add it to the request.
            Policy policy = new Policy();
            policy.withStatements(new Statement(Effect.Allow)
                    .withActions(S3Actions.ListObjects)
                    .withResources(new Resource(resourceARN)));
            getFederationTokenRequest.setPolicy(policy.toJson());

            // Get the temporary security credentials.
            GetFederationTokenResult federationTokenResult = stsClient.getFederationToken(getFederationTokenRequest);
            Credentials sessionCredentials = federationTokenResult.getCredentials();

            // Package the session credentials as a BasicSessionCredentials
            // object for an Amazon S3 client object to use.
            BasicSessionCredentials basicSessionCredentials = new BasicSessionCredentials(
                    sessionCredentials.getAccessKeyId(),
                    sessionCredentials.getSecretAccessKey(),
                    sessionCredentials.getSessionToken());
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .withCredentials(new AWSStaticCredentialsProvider(basicSessionCredentials))
                    .withRegion(clientRegion)
                    .build();

            // To verify that the client works, send a listObjects request using 
            // the temporary security credentials.
            ObjectListing objects = s3Client.listObjects(bucketName);
            System.out.println("No. of Objects = " + objects.getObjectSummaries().size());
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

You can provide temporary security credentials for your federated users and applications so that they can send authenticated requests to access your AWS resources\. When requesting these temporary credentials, you must provide a user name and an IAM policy that describes the resource permissions that you want to grant\. By default, the duration of a session is one hour\. You can explicitly set a different duration value when requesting the temporary security credentials for federated users and applications\. For information about sending authenticated requests, see [Making requests](MakingRequests.md)\.

**Note**  
When requesting temporary security credentials for federated users and applications, for added security, we suggest that you use a dedicated IAM user with only the necessary access permissions\. The temporary user you create can never get more permissions than the IAM user who requested the temporary security credentials\. For more information, see [AWS Identity and Access Management FAQs ](https://aws.amazon.com/iam/faqs/#What_are_the_best_practices_for_using_temporary_security_credentials)\.

You do the following:
+ Create an instance of the AWS Security Token Service client, `AmazonSecurityTokenServiceClient` class\. For information about providing credentials, see [Using the AWS SDK for \.NET](UsingTheMPDotNetAPI.md)\.
+ Start a session by calling the `GetFederationToken` method of the STS client\. You need to provide session information, including the user name and an IAM policy that you want to attach to the temporary credentials\. Optionally, you can provide a session duration\. This method returns your temporary security credentials\.
+ Package the temporary security credentials in an instance of the `SessionAWSCredentials` object\. You use this object to provide the temporary security credentials to your Amazon S3 client\.
+ Create an instance of the `AmazonS3Client` class by passing the temporary security credentials\. You use this client to send requests to Amazon S3\. If you send requests using expired credentials, Amazon S3 returns an error\. 

**Example**  
The following C\# example lists the keys in the specified bucket\. In the example, you obtain temporary security credentials for a two\-hour session for your federated user \(User1\), and use the credentials to send authenticated requests to Amazon S3\.   
+ For this exercise, you create an IAM user with minimal permissions\. Using the credentials of this IAM user, you request temporary credentials for others\. This example lists only the objects in a specific bucket\. Create an IAM user with the following policy attached: 

  ```
   1. {
   2.   "Statement":[{
   3.       "Action":["s3:ListBucket",
   4.         "sts:GetFederationToken*"
   5.       ],
   6.       "Effect":"Allow",
   7.       "Resource":"*"
   8.     }
   9.   ]
  10. }
  ```

  The policy allows the IAM user to request temporary security credentials and access permission only to list your AWS resources\. For more information about how to create an IAM user, see [Creating Your IAM user User and Administrators Group](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html) in the *IAM User Guide*\. 
+ Use the IAM user security credentials to test the following example\. The example sends authenticated request to Amazon S3 using temporary security credentials\. The example specifies the following policy when requesting temporary security credentials for the federated user \(User1\), which restricts access to listing objects in a specific bucket \(`YourBucketName`\)\. You must update the policy and provide your own existing bucket name\.

  ```
   1. {
   2.   "Statement":[
   3.     {
   4.       "Sid":"1",
   5.       "Action":["s3:ListBucket"],
   6.       "Effect":"Allow", 
   7.       "Resource":"arn:aws:s3:::YourBucketName"
   8.     }
   9.   ]
  10. }
  ```
+   
**Example**  

  Update the following sample and provide the bucket name that you specified in the preceding federated user access policy\. For instructions on how to create and test a working example, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\.

  ```
  using Amazon;
  using Amazon.Runtime;
  using Amazon.S3;
  using Amazon.S3.Model;
  using Amazon.SecurityToken;
  using Amazon.SecurityToken.Model;
  using System;
  using System.Collections.Generic;
  using System.Threading.Tasks;
  
  namespace Amazon.DocSamples.S3
  {
      class TempFederatedCredentialsTest
      {
          private const string bucketName = "*** bucket name ***";
          // Specify your bucket region (an example region is shown).
          private static readonly RegionEndpoint bucketRegion = RegionEndpoint.USWest2;
          private static IAmazonS3 client;
  
          public static void Main()
          {
              ListObjectsAsync().Wait();
          }
  
          private static async Task ListObjectsAsync()
          {
              try
              {
                  Console.WriteLine("Listing objects stored in a bucket");
                  // Credentials use the default AWS SDK for .NET credential search chain. 
                  // On local development machines, this is your default profile.
                  SessionAWSCredentials tempCredentials =
                      await GetTemporaryFederatedCredentialsAsync();
  
                  // Create a client by providing temporary security credentials.
                  using (client = new AmazonS3Client(bucketRegion))
                  {
                      ListObjectsRequest listObjectRequest = new ListObjectsRequest();
                      listObjectRequest.BucketName = bucketName;
  
                      ListObjectsResponse response = await client.ListObjectsAsync(listObjectRequest);
                      List<S3Object> objects = response.S3Objects;
                      Console.WriteLine("Object count = {0}", objects.Count);
  
                      Console.WriteLine("Press any key to continue...");
                      Console.ReadKey();
                  }
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
  
          private static async Task<SessionAWSCredentials> GetTemporaryFederatedCredentialsAsync()
          {
              AmazonSecurityTokenServiceConfig config = new AmazonSecurityTokenServiceConfig();
              AmazonSecurityTokenServiceClient stsClient =
                  new AmazonSecurityTokenServiceClient(
                                               config);
  
              GetFederationTokenRequest federationTokenRequest =
                                       new GetFederationTokenRequest();
              federationTokenRequest.DurationSeconds = 7200;
              federationTokenRequest.Name = "User1";
              federationTokenRequest.Policy = @"{
                 ""Statement"":
                 [
                   {
                     ""Sid"":""Stmt1311212314284"",
                     ""Action"":[""s3:ListBucket""],
                     ""Effect"":""Allow"",
                     ""Resource"":""arn:aws:s3:::" + bucketName + @"""
                    }
                 ]
               }
              ";
  
              GetFederationTokenResponse federationTokenResponse =
                          await stsClient.GetFederationTokenAsync(federationTokenRequest);
              Credentials credentials = federationTokenResponse.Credentials;
  
              SessionAWSCredentials sessionCredentials =
                  new SessionAWSCredentials(credentials.AccessKeyId,
                                            credentials.SecretAccessKey,
                                            credentials.SessionToken);
              return sessionCredentials;
          }
      }
  }
  ```

------
#### [ PHP ]

This topic explains how to use classes from version 3 of the AWS SDK for PHP to request temporary security credentials for federated users and applications and use them to access resources stored in Amazon S3\. It assumes that you are already following the instructions for [Using the AWS SDK for PHP and Running PHP Examples](UsingTheMPphpAPI.md) and have the AWS SDK for PHP properly installed\. 

You can provide temporary security credentials to your federated users and applications so they can send authenticated requests to access your AWS resources\. When requesting these temporary credentials, you must provide a user name and an IAM policy that describes the resource permissions that you want to grant\. These credentials expire when the session duration expires\. By default, the session duration is one hour\. You can explicitly set a different value for the duration when requesting the temporary security credentials for federated users and applications\. For more information about temporary security credentials, see [Temporary Security Credentials](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) in the *IAM User Guide*\. For information about providing temporary security credentials to your federated users and applications, see [Making requests](MakingRequests.md)\.

For added security when requesting temporary security credentials for federated users and applications, we recommend using a dedicated IAM user with only the necessary access permissions\. The temporary user you create can never get more permissions than the IAM user who requested the temporary security credentials\. For information about identity federation, see [AWS Identity and Access Management FAQs](https://aws.amazon.com/iam/faqs/#What_are_the_best_practices_for_using_temporary_security_credentials)\.

For information about running the PHP examples in this guide, see [Running PHP Examples](UsingTheMPphpAPI.md#running-php-samples)\.

**Example**  
The following PHP example lists keys in the specified bucket\. In the example, you obtain temporary security credentials for an hour session for your federated user \(User1\)\. Then you use the temporary security credentials to send authenticated requests to Amazon S3\.   
For added security when requesting temporary credentials for others, you use the security credentials of an IAM user who has permissions to request temporary security credentials\. To ensure that the IAM user grants only the minimum application\-specific permissions to the federated user, you can also limit the access permissions of this IAM user\. This example lists only objects in a specific bucket\. Create an IAM user with the following policy attached:   

```
 1. {
 2.   "Statement":[{
 3.       "Action":["s3:ListBucket",
 4.         "sts:GetFederationToken*"
 5.       ],
 6.       "Effect":"Allow",
 7.       "Resource":"*"
 8.     }
 9.   ]
10. }
```
The policy allows the IAM user to request temporary security credentials and access permission only to list your AWS resources\. For more information about how to create an IAM user, see [Creating Your First IAM user and Administrators Group](https://docs.aws.amazon.com/IAM/latest/UserGuide/getting-started_create-admin-group.html) in the *IAM User Guide*\.   
You can now use the IAM user security credentials to test the following example\. The example sends an authenticated request to Amazon S3 using temporary security credentials\. When requesting temporary security credentials for the federated user \(User1\), the example specifies the following policy, which restricts access to list objects in a specific bucket\. Update the policy with your bucket name\.  

```
 1. {
 2.   "Statement":[
 3.     {
 4.       "Sid":"1",
 5.       "Action":["s3:ListBucket"],
 6.       "Effect":"Allow", 
 7.       "Resource":"arn:aws:s3:::YourBucketName"
 8.     }
 9.   ]
10. }
```
In the following example, when specifying the policy resource, replace `YourBucketName` with the name of your bucket\.:  

```
 require 'vendor/autoload.php';

use Aws\Sts\StsClient;
use Aws\S3\S3Client;
use Aws\S3\Exception\S3Exception;

$bucket = '*** Your Bucket Name ***';

// In real applications, the following code is part of your trusted code. It has
// the security credentials that you use to obtain temporary security credentials.
$sts = new StsClient(
    [
    'version' => 'latest',
    'region' => 'us-east-1']
);

// Fetch the federated credentials.
$sessionToken = $sts->getFederationToken([
    'Name'              => 'User1',
    'DurationSeconds'    => '3600',
    'Policy'            => json_encode([
        'Statement' => [
            'Sid'              => 'randomstatementid' . time(),
            'Action'           => ['s3:ListBucket'],
            'Effect'           => 'Allow',
            'Resource'         => 'arn:aws:s3:::' . $bucket
        ]
    ])
]);

// The following will be part of your less trusted code. You provide temporary
// security credentials so the code can send authenticated requests to Amazon S3.

$s3 = new S3Client([
    'region' => 'us-east-1',
    'version' => 'latest',
    'credentials' => [
        'key'    => $sessionToken['Credentials']['AccessKeyId'],
        'secret' => $sessionToken['Credentials']['SecretAccessKey'],
        'token'  => $sessionToken['Credentials']['SessionToken']
    ]
]);

try {
    $result = $s3->listObjects([
        'Bucket' => $bucket
    ]);
} catch (S3Exception $e) {
    echo $e->getMessage() . PHP_EOL;
}
```

------
#### [ Ruby ]

You can provide temporary security credentials for your federated users and applications so that they can send authenticated requests to access your AWS resources\. When requesting temporary credentials from the IAM service, you must provide a user name and an IAM policy that describes the resource permissions that you want to grant\. By default, the session duration is one hour\. However, if you are requesting temporary credentials using IAM user credentials, you can explicitly set a different duration value when requesting the temporary security credentials for federated users and applications\. For information about temporary security credentials for your federated users and applications, see [Making requests](MakingRequests.md)\.

**Note**  
For added security when you request temporary security credentials for federated users and applications, you might want to use a dedicated IAM user with only the necessary access permissions\. The temporary user you create can never get more permissions than the IAM user who requested the temporary security credentials\. For more information, see [AWS Identity and Access Management FAQs ](https://aws.amazon.com/iam/faqs/#What_are_the_best_practices_for_using_temporary_security_credentials)\.

**Example**  
The following Ruby code example allows a federated user with a limited set of permissions to lists keys in the specified bucket\.   

```
# Prerequisites:
#  - An existing Amazon S3 bucket.

require "aws-sdk-s3"
require "aws-sdk-iam"
require "json"

# Checks to see whether a user exists in IAM; otherwise,
# creates the user.
#
# @param iam [Aws::IAM::Client] An initialized IAM client.
# @param user_name [String] The user's name.
# @return [Aws::IAM::Types::User] The existing or new user.
# @example
#   iam = Aws::IAM::Client.new(region: 'us-west-2')
#   user = get_user(iam, 'my-user')
#   exit 1 unless user.user_name
#   puts "User's name: #{user.user_name}"
def get_user(iam, user_name)
  puts "Checking for a user with the name '#{user_name}'..."
  response = iam.get_user(user_name: user_name)
  puts "A user with the name '#{user_name}' already exists."
  return response.user
# If the user doesn't exist, create them.
rescue Aws::IAM::Errors::NoSuchEntity
  puts "A user with the name '#{user_name}' doesn't exist. Creating this user..."
  response = iam.create_user(user_name: user_name)
  iam.wait_until(:user_exists, user_name: user_name)
  puts "Created user with the name '#{user_name}'."
  return response.user
rescue StandardError => e
  puts "Error while accessing or creating the user named '#{user_name}': #{e.message}"
end

# Gets temporary AWS credentials for an IAM user with the specified permissions.
#
# @param sts [Aws::STS::Client] An initialized AWS STS client.
# @param duration_seconds [Integer] The number of seconds for valid credentials.
# @param user_name [String] The user's name.
# @param policy [Hash] The access policy.
# @return [Aws::STS::Types::Credentials] AWS credentials for API authentication.
# @example
#   sts = Aws::STS::Client.new(region: 'us-west-2')
#   credentials = get_temporary_credentials(sts, duration_seconds, user_name,
#     {
#       'Version' => '2012-10-17',
#       'Statement' => [
#         'Sid' => 'Stmt1',
#         'Effect' => 'Allow',
#         'Action' => 's3:ListBucket',
#         'Resource' => 'arn:aws:s3:::doc-example-bucket'
#       ]
#     }
#   )
#   exit 1 unless credentials.access_key_id
#   puts "Access key ID: #{credentials.access_key_id}"
def get_temporary_credentials(sts, duration_seconds, user_name, policy)
  response = sts.get_federation_token(
    duration_seconds: duration_seconds,
    name: user_name,
    policy: policy.to_json
  )
  return response.credentials
rescue StandardError => e
  puts "Error while getting federation token: #{e.message}"
end

# Lists the keys and ETags for the objects in an Amazon S3 bucket.
#
# @param s3_client [Aws::S3::Client] An initialized Amazon S3 client.
# @param bucket_name [String] The bucket's name.
# @return [Boolean] true if the objects were listed; otherwise, false.
# @example
#   s3_client = Aws::S3::Client.new(region: 'us-west-2')
#   exit 1 unless list_objects_in_bucket?(s3_client, 'doc-example-bucket')
def list_objects_in_bucket?(s3_client, bucket_name)
  puts "Accessing the contents of the bucket named '#{bucket_name}'..."
  response = s3_client.list_objects_v2(
    bucket: bucket_name,
    max_keys: 50
  )

  if response.count.positive?
    puts "Contents of the bucket named '#{bucket_name}' (first 50 objects):"
    puts "Name => ETag"
    response.contents.each do |obj|
      puts "#{obj.key} => #{obj.etag}"
    end
  else
    puts "No objects in the bucket named '#{bucket_name}'."
  end
  return true
rescue StandardError => e
  puts "Error while accessing the bucket named '#{bucket_name}': #{e.message}"
end

# Full example call:
# Replace us-west-2 with the AWS Region you're using for Amazon S3.
def run_me
  region = "us-west-2"
  user_name = "my-user"
  bucket_name = "doc-example-bucket"

  iam = Aws::IAM::Client.new(region: region)
  user = get_user(iam, user_name)

  exit 1 unless user.user_name

  puts "User's name: #{user.user_name}"
  sts = Aws::STS::Client.new(region: region)
  credentials = get_temporary_credentials(sts, 3600, user_name,
    {
      "Version" => "2012-10-17",
      "Statement" => [
        "Sid" => "Stmt1",
        "Effect" => "Allow",
        "Action" => "s3:ListBucket",
        "Resource" => "arn:aws:s3:::#{bucket_name}"
      ]
    }
  )

  exit 1 unless credentials.access_key_id

  puts "Access key ID: #{credentials.access_key_id}"
  s3_client = Aws::S3::Client.new(region: region, credentials: credentials)

  exit 1 unless list_objects_in_bucket?(s3_client, bucket_name)
end

run_me if $PROGRAM_NAME == __FILE__
```

------

## Related resources<a name="RelatedResources005"></a>
+ [Developing with Amazon S3 using the AWS SDKs, and explorers](UsingAWSSDK.md)
+ [AWS SDK for PHP for Amazon S3 Aws\\S3\\S3Client Class](https://docs.aws.amazon.com/aws-sdk-php/v3/api/class-Aws.S3.S3Client.html) 
+ [AWS SDK for PHP Documentation](http://aws.amazon.com/documentation/sdk-for-php/)