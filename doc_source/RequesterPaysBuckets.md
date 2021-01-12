--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Configuring Requester Pays buckets for storage transfers and usage<a name="RequesterPaysBuckets"></a>

In general, bucket owners pay for all Amazon S3 storage and data transfer costs that are associated with their bucket\. However, you can configure a bucket to be a *Requester Pays* bucket\. With Requester Pays buckets, the requester instead of the bucket owner pays the cost of the request and the data download from the bucket\. The bucket owner always pays the cost of storing data\. 

Typically, you configure buckets to be Requester Pays buckets when you want to share data but not incur charges associated with others accessing the data\. For example, you might use Requester Pays buckets when making available large datasets, such as zip code directories, reference data, geospatial information, or web crawling data\. 

**Important**  
If you enable Requester Pays on a bucket, anonymous access to that bucket is not allowed\.

You must authenticate all requests involving Requester Pays buckets\. The request authentication enables Amazon S3 to identify and charge the requester for their use of the Requester Pays bucket\. 

When the requester assumes an AWS Identity and Access Management \(IAM\) role before making their request, the account to which the role belongs is charged for the request\. For more information about IAM roles, see [IAM roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) in the *IAM User Guide*\. 

After you configure a bucket to be a Requester Pays bucket, requesters must include `x-amz-request-payer` in their requests either in the header, for POST, GET and HEAD requests, or as a parameter in a REST request to show that they understand that they will be charged for the request and the data download\.

Requester Pays buckets do not support the following:
+ Anonymous requests
+ BitTorrent
+ SOAP requests
+ Using a Requester Pays bucket as the target bucket for end\-user logging, or vice versa\. However, you can turn on end\-user logging on a Requester Pays bucket where the target bucket is not a Requester Pays bucket\. 

**Topics**
+ [How Requester Pays charges work](#ChargeDetails)
+ [Enabling Requester Pays on a bucket](RequesterPaysExamples.md)

## How Requester Pays charges work<a name="ChargeDetails"></a>

The charge for successful Requester Pays requests is straightforward: The requester pays for the data transfer and the request, and the bucket owner pays for the data storage\. However, the bucket owner is charged for the request under the following conditions:
+ The requester doesn't include the parameter `x-amz-request-payer` in the header \(GET, HEAD, or POST\) or as a parameter \(REST\) in the request \(HTTP code 403\)\.
+ Request authentication fails \(HTTP code 403\)\.
+ The request is anonymous \(HTTP code 403\)\.
+ The request is a SOAP request\.

For more information about configuring Requester Pays buckets, see [Enabling Requester Pays on a bucket](RequesterPaysExamples.md)\.