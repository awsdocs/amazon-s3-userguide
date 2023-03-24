# Virtual hosting of buckets<a name="VirtualHosting"></a>

Virtual hosting is the practice of serving multiple websites from a single web server\. One way to differentiate sites in your Amazon S3 REST API requests is by using the apparent hostname of the Request\-URI instead of just the path name part of the URI\. An ordinary Amazon S3 REST request specifies a bucket by using the first slash\-delimited component of the Request\-URI path\. Instead, you can use Amazon S3 virtual hosting to address a bucket in a REST API call by using the HTTP `Host` header\. In practice, Amazon S3 interprets `Host` as meaning that most buckets are automatically accessible for limited types of requests at `https://bucket-name.s3.region-code.amazonaws.com`\. For a complete list of Amazon S3 Regions and endpoints, see [Amazon S3 endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/s3.html) in the *Amazon Web Services General Reference*\.

Virtual hosting also has other benefits\. By naming your bucket after your registered domain name and by making that name a DNS alias for Amazon S3, you can completely customize the URL of your Amazon S3 resources, for example, `http://my.bucket-name.com/`\. You can also publish to the "root directory" of your bucket's virtual server\. This ability can be important because many existing applications search for files in this standard location\. For example, `favicon.ico`, `robots.txt`, and `crossdomain.xml` are all expected to be found at the root\. 

**Important**  
When you're using virtual\-hosted–style buckets with SSL, the SSL wildcard certificate matches only buckets that do not contain dots \(`.`\)\. To work around this limitation, use HTTP or write your own certificate\-verification logic\. For more information, see [Amazon S3 Path Deprecation Plan](http://aws.amazon.com/blogs/aws/amazon-s3-path-deprecation-plan-the-rest-of-the-story/) on the *AWS News Blog\.*

**Topics**
+ [Path\-style requests](#path-style-access)
+ [Virtual\-hosted–style requests](#virtual-hosted-style-access)
+ [HTTP `Host` header bucket specification](#VirtualHostingSpecifyBucket)
+ [Examples](#VirtualHostingExamples)
+ [Customizing Amazon S3 URLs with CNAME records](#VirtualHostingCustomURLs)
+ [How to associate a hostname with an Amazon S3 bucket](#VirtualHostingCustomURLsHowTo)
+ [Limitations](#VirtualHostingLimitations)
+ [Backward compatibility](#VirtualHostingBackwardsCompatibility)

## Path\-style requests<a name="path-style-access"></a>

Currently, Amazon S3 supports both virtual\-hosted–style and path\-style URL access in all AWS Regions\. However, path\-style URLs will be discontinued in the future\. For more information, see the following **Important** note\.

In Amazon S3, path\-style URLs use the following format:

```
https://s3.region-code.amazonaws.com/bucket-name/key-name
```

For example, if you create a bucket named `DOC-EXAMPLE-BUCKET1` in the US West \(Oregon\) Region, and you want to access the `puppy.jpg` object in that bucket, you can use the following path\-style URL:

```
https://s3.us-west-2.amazonaws.com/DOC-EXAMPLE-BUCKET1/puppy.jpg
```

**Important**  
Update \(September 23, 2020\) – To make sure that customers have the time that they need to transition to virtual\-hosted–style URLs, we have decided to delay the deprecation of path\-style URLs\. For more information, see [Amazon S3 Path Deprecation Plan – The Rest of the Story](https://aws.amazon.com/blogs/aws/amazon-s3-path-deprecation-plan-the-rest-of-the-story/) in the *AWS News Blog*\.

**Warning**  
When hosting website content that will be accessed from a web browser, avoid using path\-style URLs, which might interfere with the browser same origin security model\. To host website content, we recommend that you use either S3 website endpoints or a CloudFront distribution\. For more information, see [Website endpoints](WebsiteEndpoints.md) and [ Deploy a React\-based single\-page application to Amazon S3 and CloudFront](https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/deploy-a-react-based-single-page-application-to-amazon-s3-and-cloudfront.html) in the *AWS Perspective Guidance Patterns*\.

## Virtual\-hosted–style requests<a name="virtual-hosted-style-access"></a>

In a virtual\-hosted–style URI, the bucket name is part of the domain name in the URL\.

Amazon S3 virtual\-hosted–style URLs use the following format:

```
https://bucket-name.s3.region-code.amazonaws.com/key-name
```

In this example, `DOC-EXAMPLE-BUCKET1` is the bucket name, US West \(Oregon\) is the Region, and `puppy.png` is the key name:

```
https://DOC-EXAMPLE-BUCKET1.s3.us-west-2.amazonaws.com/puppy.png
```

## HTTP `Host` header bucket specification<a name="VirtualHostingSpecifyBucket"></a>

As long as your `GET` request does not use the SSL endpoint, you can specify the bucket for the request by using the HTTP `Host` header\. The `Host` header in a REST request is interpreted as follows: 
+ If the `Host` header is omitted or its value is `s3.region-code.amazonaws.com`, the bucket for the request will be the first slash\-delimited component of the Request\-URI, and the key for the request will be the rest of the Request\-URI\. This is the ordinary method, as illustrated by the first and second examples in this section\. Omitting the `Host` header is valid only for HTTP 1\.0 requests\. 
+ Otherwise, if the value of the `Host` header ends in `.s3.region-code.amazonaws.com`, the bucket name is the leading component of the `Host` header's value up to `.s3.region-code.amazonaws.com`\. The key for the request is the Request\-URI\. This interpretation exposes buckets as subdomains of `.s3.region-code.amazonaws.com`, as illustrated by the third and fourth examples in this section\. 
+ Otherwise, the bucket for the request is the lowercase value of the `Host` header, and the key for the request is the Request\-URI\. This interpretation is useful when you have registered the same DNS name as your bucket name and have configured that name to be a canonical name \(CNAME\) alias for Amazon S3\. The procedure for registering domain names and configuring CNAME DNS records is beyond the scope of this guide, but the result is illustrated by the final example in this section\.

## Examples<a name="VirtualHostingExamples"></a>

This section provides example URLs and requests\.

**Example – Path\-style URLs and requests**  
This example uses the following:  
+ Bucket Name ‐ `example.com`
+ Region ‐ US East \(N\. Virginia\) 
+ Key Name ‐ `homepage.html`
The URL is as follows:  

```
1. http://s3.us-east-1.amazonaws.com/example.com/homepage.html
```
The request is as follows:  

```
1. GET /example.com/homepage.html HTTP/1.1
2. Host: s3.us-east-1.amazonaws.com
```
The request with HTTP 1\.0 and omitting the `Host` header is as follows:  

```
1. GET /example.com/homepage.html HTTP/1.0
```

For information about DNS\-compatible names, see [Limitations](#VirtualHostingLimitations)\. For more information about keys, see [Keys](Welcome.md#BasicsKeys)\.

**Example – Virtual\-hosted–style URLs and requests**  
This example uses the following:  
+ **Bucket name** ‐ `DOC-EXAMPLE-BUCKET1.eu` 
+ **Region** ‐ Europe \(Ireland\) 
+ **Key name** ‐ `homepage.html`
The URL is as follows:  

```
1. http://DOC-EXAMPLE-BUCKET1.eu.s3.eu-west-1.amazonaws.com/homepage.html
```
The request is as follows:  

```
1. GET /homepage.html HTTP/1.1
2. Host: DOC-EXAMPLE-BUCKET1.eu.s3.eu-west-1.amazonaws.com
```

**Example – CNAME alias method**  
To use this method, you must configure your DNS name as a CNAME alias for `bucket-name.s3.us-east-1.amazonaws.com`\. For more information, see [Customizing Amazon S3 URLs with CNAME records](#VirtualHostingCustomURLs)\.   
This example uses the following:  
+ Bucket Name ‐ `example.com` 
+ **Key name** ‐ `homepage.html`
The URL is as follows:  

```
1. http://www.example.com/homepage.html
```
The example is as follows:  

```
1. GET /homepage.html HTTP/1.1
2. Host: www.example.com
```

## Customizing Amazon S3 URLs with CNAME records<a name="VirtualHostingCustomURLs"></a>

Depending on your needs, you might not want `s3.region-code.amazonaws.com` to appear on your website or service\. For example, if you're hosting website images on Amazon S3, you might prefer `http://images.example.com/` instead of `http://images.example.com.s3.us-east-1.amazonaws.com/`\. Any bucket with a DNS\-compatible name can be referenced as follows: ` http://BucketName.s3.Region.amazonaws.com/[Filename]`, for example, `http://images.example.com.s3.us-east-1.amazonaws.com/mydog.jpg`\. By using CNAME, you can map `images.example.com` to an Amazon S3 hostname so that the previous URL could become `http://images.example.com/mydog.jpg`\. 

Your bucket name must be the same as the CNAME\. For example, if you create a CNAME to map `images.example.com` to `images.example.com.s3.us-east-1.amazonaws.com`, both `http://images.example.com/filename` and `http://images.example.com.s3.us-east-1.amazonaws.com/filename` will be the same\.

The CNAME DNS record should alias your domain name to the appropriate virtual hosted–style hostname\. For example, if your bucket name and domain name are `images.example.com` and your bucket is in the US East \(N\. Virginia\) Region, the CNAME record should alias to `images.example.com.s3.us-east-1.amazonaws.com`\. 

```
1. images.example.com CNAME 			images.example.com.s3.us-east-1.amazonaws.com.
```

Amazon S3 uses the hostname to determine the bucket name\. So the CNAME and the bucket name must be the same\. For example, suppose that you have configured `www.example.com` as a CNAME for `www.example.com.s3.us-east-1.amazonaws.com`\. When you access `http://www.example.com`, Amazon S3 receives a request similar to the following:

**Example**  

```
1. GET / HTTP/1.1
2. Host: www.example.com
3. Date: date
4. Authorization: signatureValue
```

Amazon S3 sees only the original hostname `www.example.com` and is unaware of the CNAME mapping used to resolve the request\. 

You can use any Amazon S3 endpoint in a CNAME alias\. For example, `s3.ap-southeast-1.amazonaws.com` can be used in CNAME aliases\. For more information about endpoints, see [Request Endpoints](MakingRequests.md#RequestEndpoints)\. To create a static website by using a custom domain, see [Tutorial: Configuring a static website using a custom domain registered with Route 53](website-hosting-custom-domain-walkthrough.md)\.

**Important**  
When using custom URLs with CNAMEs, you will need to ensure a matching bucket exists for any CNAME or alias record you configure\. For example, if you create DNS entries for `www.example.com` and `login.example.com` to publish web content using S3, you will need to create both buckets `www.example.com` and `login.example.com`\.  
When a CNAME or alias records is configured pointing to an S3 endpoint without a matching bucket, any AWS user can create that bucket and publish content under the configured alias, even if ownership is not the same\.  
For the same reason, we recommend that you change or remove the corresponding CNAME or alias when deleting a bucket\.

## How to associate a hostname with an Amazon S3 bucket<a name="VirtualHostingCustomURLsHowTo"></a>

**To associate a hostname with an Amazon S3 bucket by using a CNAME alias**

1. Select a hostname that belongs to a domain that you control\. 

   This example uses the `images` subdomain of the `example.com` domain\.

1. Create a bucket that matches the hostname\. 

   In this example, the host and bucket names are `images.example.com`\. The bucket name must *exactly* match the hostname\. 

1. Create a CNAME DNS record that defines the hostname as an alias for the Amazon S3 bucket\. 

   For example:

   `images.example.com CNAME images.example.com.s3.us-west-2.amazonaws.com`
**Important**  
For request\-routing reasons, the CNAME DNS record must be defined exactly as shown in the preceding example\. Otherwise, it might appear to operate correctly, but it will eventually result in unpredictable behavior\.

   The procedure for configuring CNAME DNS records depends on your DNS server or DNS provider\. For specific information, see your server documentation or contact your provider\.

## Limitations<a name="VirtualHostingLimitations"></a>

 SOAP support over HTTP is deprecated, but SOAP is still available over HTTPS\. New Amazon S3 features are not supported for SOAP\. Instead of using SOAP, we recommend that you use either the REST API or the AWS SDKs\. 

## Backward compatibility<a name="VirtualHostingBackwardsCompatibility"></a>

The following sections cover various aspects of Amazon S3 backward compatibility that relate to path\-style and virtual\-hosted–style URL requests\.

### Legacy endpoints<a name="s3-legacy-endpoints"></a>

Some Regions support legacy endpoints\. You might see these endpoints in your server access logs or AWS CloudTrail logs\. For more information, review the following information\. For a complete list of Amazon S3 Regions and endpoints, see [Amazon S3 endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/s3.html) in the *Amazon Web Services General Reference*\.

**Important**  
Although you might see legacy endpoints in your logs, we recommend that you always use the standard endpoint syntax to access your buckets\.   
Amazon S3 virtual\-hosted–style URLs use the following format:  

```
https://bucket-name.s3.region-code.amazonaws.com/key-name
```
In Amazon S3, path\-style URLs use the following format:  

```
https://s3.region-code.amazonaws.com/bucket-name/key-name
```

#### s3‐Region<a name="s3-dash-region"></a>

Some older Amazon S3 Regions support endpoints that contain a dash \(`-`\) between `s3` and the Region code \(for example, `s3‐us-west-2`\), instead of a dot \(for example, `s3.us-west-2`\)\. If your bucket is in one of these Regions, you might see the following endpoint format in your server access logs or CloudTrail logs:

```
https://bucket-name.s3-region-code.amazonaws.com
```

In this example, the bucket name is DOC\-EXAMPLE\-BUCKET1 and the Region is US West \(Oregon\):

```
https://DOC-EXAMPLE-BUCKET1.s3-us-west-2.amazonaws.com
```

#### Legacy global endpoint<a name="deprecated-global-endpoint"></a>

For some Regions, you can use the legacy global endpoint to construct requests that do not specify a Region\-specific endpoint\. The legacy global endpoint point is as follows:

```
bucket-name.s3.amazonaws.com
```

In your server access logs or CloudTrail logs, you might see requests that use the legacy global endpoint\. In this example, the bucket name is `DOC-EXAMPLE-BUCKET1` and the legacy global endpoint is: 

```
https://DOC-EXAMPLE-BUCKET1.s3.amazonaws.com
```

**Virtual\-hosted–style requests for US East \(N\. Virginia\)**  
Requests made with the legacy global endpoint go to the US East \(N\. Virginia\) Region by default\. Therefore, the legacy global endpoint is sometimes used in place of the Regional endpoint for US East \(N\. Virginia\)\. If you create a bucket in US East \(N\. Virginia\) and use the global endpoint, Amazon S3 routes your request to this Region by default\. 

**Virtual\-hosted–style requests for other Regions**  
The legacy global endpoint is also used for virtual\-hosted–style requests in other supported Regions\. If you create a bucket in a Region that was launched before March 20, 2019, and use the legacy global endpoint, Amazon S3 updates the DNS record to reroute the request to the correct location, which might take time\. In the meantime, the default rule applies, and your virtual\-hosted–style request goes to the US East \(N\. Virginia\) Region\. Amazon S3 then redirects it with an HTTP 307 Temporary Redirect to the correct Region\. 

For S3 buckets in Regions launched after March 20, 2019, the DNS server doesn't route your request directly to the AWS Region where your bucket resides\. It returns an HTTP 400 Bad Request error instead\. For more information, see [Making requests](MakingRequests.md)\.

**Path\-style requests**  
For the US East \(N\. Virginia\) Region, you can use the legacy global endpoint for path\-style requests\. 

For all other Regions, the path\-style syntax requires that you use the Region\-specific endpoint when attempting to access a bucket\. If you try to access a bucket with the legacy global endpoint or another endpoint that is different than the one for the Region where the bucket resides, you receive an HTTP response code 307 Temporary Redirect error and a message that indicates the correct URI for your resource\. For example, if you use `https://s3.amazonaws.com/bucket-name` for a bucket that was created in the US West \(Oregon\) Region, you will receive an HTTP 307 Temporary Redirect error\.