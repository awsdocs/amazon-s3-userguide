--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Enabling website hosting<a name="EnableWebsiteHosting"></a>

When you configure a bucket as a static website, you must enable static website hosting, configure an index document, and set permissions\.

You can enable static website hosting using the Amazon S3 console, REST API, the AWS SDKs, the AWS CLI, or AWS CloudFormation\.

To configure your website with a custom domain, see [Configuring a static website using a custom domain registered with Route 53](website-hosting-custom-domain-walkthrough.md)\.

## Using the S3 console<a name="HowDoIWebsiteConfiguration"></a>

**To enable static website hosting**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want to enable static website hosting for\.

1. Choose **Properties**\.

1. Under **Static website hosting**, choose **Edit**\.

1. Choose **Use this bucket to host a website**\. 

1. Under **Static website hosting**, choose **Enable**\.

1. In **Index document**, enter the file name of the index document, typically `index.html`\. 

   The index document name is case sensitive and must exactly match the file name of the HTML index document that you plan to upload to your S3 bucket\. When you configure a bucket for website hosting, you must specify an index document\. Amazon S3 returns this index document when requests are made to the root domain or any of the subfolders\. For more information, see [Configuring an index document](IndexDocumentSupport.md)\.

1. To provide your own custom error document for 4XX class errors, in **Error document**, enter the custom error document file name\. 

   The error document name is case sensitive and must exactly match the file name of the HTML error document that you plan to upload to your S3 bucket\. If you don't specify a custom error document and an error occurs, Amazon S3 returns a default HTML error document\. For more information, see [Configuring a custom error document](CustomErrorDocSupport.md)\.

1. \(Optional\) If you want to specify advanced redirection rules, in **Redirection rules**, enter XML to describe the rules\.

   For example, you can conditionally route requests according to specific object key names or prefixes in the request\. For more information, see [Configuring advanced conditional redirects](how-to-page-redirect.md#advanced-conditional-redirects)\.

1. Choose **Save changes**\.

   Amazon S3 enables static website hosting for your bucket\. At the bottom of the page, under **Static website hosting**, you see the website endpoint for your bucket\.

1. Under **Static website hosting**, note the **Endpoint**\.

   The **Endpoint** is the Amazon S3 website endpoint for your bucket\. After you finish configuring your bucket as a static website, you can use this endpoint to test your website\.

## Using the REST API<a name="ConfigWebSiteREST"></a>

For more information about sending REST requests directly to enable static website hosting, see the following sections in the Amazon Simple Storage Service API Reference:
+ [PUT Bucket website](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTwebsite.html)
+ [GET Bucket website](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketGETwebsite.html)
+ [DELETE Bucket website](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketDELETEwebsite.html)

## Using the AWS SDKs<a name="ManagingBucketWebsiteConfig"></a>

To host a static website on Amazon S3, you configure an Amazon S3 bucket for website hosting and then upload your website content to the bucket\. You can also use the AWS SDKs to create, update, and delete the website configuration programmatically\. The SDKs provide wrapper classes around the Amazon S3 REST API\. If your application requires it, you can send REST API requests directly from your application\. 

------
#### [ \.NET ]

The following example shows how to use the AWS SDK for \.NET to manage website configuration for a bucket\. To add a website configuration to a bucket, you provide a bucket name and a website configuration\. The website configuration must include an index document and can contain an optional error document\. These documents must be stored in the bucket\. For more information, see [PUT Bucket website](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTBucketPUTwebsite.html)\. For more information about the Amazon S3 website feature, see [Hosting a static website using Amazon S3](WebsiteHosting.md)\. 

The following C\# code example adds a website configuration to the specified bucket\. The configuration specifies both the index document and the error document names\. For instructions on how to create and test a working sample, see [Running the Amazon S3 \.NET Code Examples](UsingTheMPDotNetAPI.md#TestingDotNetApiSamples)\.

```
using Amazon;
using Amazon.S3;
using Amazon.S3.Model;
using System;
using System.Threading.Tasks;

namespace Amazon.DocSamples.S3
{
    class WebsiteConfigTest
    {
        private const string bucketName = "*** bucket name ***";
        private const string indexDocumentSuffix = "*** index object key ***"; // For example, index.html.
        private const string errorDocument = "*** error object key ***"; // For example, error.html.
        // Specify your bucket region (an example region is shown).
        private static readonly RegionEndpoint bucketRegion = RegionEndpoint.USWest2;
        private static IAmazonS3 client;
        public static void Main()
        {
            client = new AmazonS3Client(bucketRegion);
            AddWebsiteConfigurationAsync(bucketName, indexDocumentSuffix, errorDocument).Wait();
        }

        static async Task AddWebsiteConfigurationAsync(string bucketName,
                                            string indexDocumentSuffix,
                                            string errorDocument)
        {
            try
            {
                // 1. Put the website configuration.
                PutBucketWebsiteRequest putRequest = new PutBucketWebsiteRequest()
                {
                    BucketName = bucketName,
                    WebsiteConfiguration = new WebsiteConfiguration()
                    {
                        IndexDocumentSuffix = indexDocumentSuffix,
                        ErrorDocument = errorDocument
                    }
                };
                PutBucketWebsiteResponse response = await client.PutBucketWebsiteAsync(putRequest);

                // 2. Get the website configuration.
                GetBucketWebsiteRequest getRequest = new GetBucketWebsiteRequest()
                {
                    BucketName = bucketName
                };
                GetBucketWebsiteResponse getResponse = await client.GetBucketWebsiteAsync(getRequest);
                Console.WriteLine("Index document: {0}", getResponse.WebsiteConfiguration.IndexDocumentSuffix);
                Console.WriteLine("Error document: {0}", getResponse.WebsiteConfiguration.ErrorDocument);
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
    }
}
```

------
#### [ PHP ]

The following PHP example adds a website configuration to the specified bucket\. The `create_website_config` method explicitly provides the index document and error document names\. The example also retrieves the website configuration and prints the response\. For more information about the Amazon S3 website feature, see [Hosting a static website using Amazon S3](WebsiteHosting.md)\.

 For instructions on creating and testing a working sample, see [Using the AWS SDK for PHP and Running PHP Examples](UsingTheMPphpAPI.md)\. 

```
 require 'vendor/autoload.php';

use Aws\S3\S3Client;

$bucket = '*** Your Bucket Name ***';
                
$s3 = new S3Client([
    'version' => 'latest',
    'region'  => 'us-east-1'
]);

         
// Add the website configuration.
$s3->putBucketWebsite([
    'Bucket'                => $bucket,
    'WebsiteConfiguration'  => [
        'IndexDocument' => ['Suffix' => 'index.html'],
        'ErrorDocument' => ['Key' => 'error.html']
    ]
]);
        
// Retrieve the website configuration.
$result = $s3->getBucketWebsite([
    'Bucket' => $bucket
]);
echo $result->getPath('IndexDocument/Suffix');
        
// Delete the website configuration.
$s3->deleteBucketWebsite([
    'Bucket' => $bucket
]);
```

------

## Using the AWS CLI<a name="enabling-website-cli"></a>

For more information about using the AWS CLI to configure an S3 bucket as a static website, see [website](https://docs.aws.amazon.com/cli/latest/reference/s3/website.html) in the *AWS CLI Command Reference*\.

Next, you must configure your index document and set permissions\. For information, see [Configuring an index document](IndexDocumentSupport.md) and [Setting permissions for website access](WebsiteAccessPermissionsReqd.md)\. 

You can also optionally configure an [error document](CustomErrorDocSupport.md), [web traffic logging](LoggingWebsiteTraffic.md), or a [redirect](how-to-page-redirect.md)\.