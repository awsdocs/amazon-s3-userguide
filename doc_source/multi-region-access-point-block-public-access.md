# Blocking public access with Amazon S3 Multi\-Region Access Points<a name="multi-region-access-point-block-public-access"></a>

Each Multi\-Region Access Point has distinct settings for Amazon S3 Block Public Access\. These settings operate in conjunction with the Block Public Access settings for the AWS account that owns the Multi\-Region Access Point and the underlying buckets\. 

When Amazon S3 authorizes a request, it applies the most restrictive combination of these settings\. If the Block Public Access settings for any of these resources \(the Multi\-Region Access Point owner account, the underlying bucket, or the bucket owner account\) block access for the requested action or resource, Amazon S3 rejects the request\.

We recommend that you enable all Block Public Access settings unless you have a specific need to disable any of them\. By default, all Block Public Access settings are enabled for a Multi\-Region Access Point\. If Block Public Access is enabled, the Multi\-Region Access Point can't accept internet\-based requests\.

**Important**  
You can't change the Block Public Access settings for a Multi\-Region Access Point after it has been created\. 

 For more information about Amazon S3 Block Public Access, see [Blocking public access to your Amazon S3 storage](access-control-block-public-access.md)\. 