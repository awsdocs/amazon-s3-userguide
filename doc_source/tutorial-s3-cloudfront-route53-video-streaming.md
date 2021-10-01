# Tutorial: Hosting on\-demand streaming video with Amazon S3, CloudFront, and Route 53<a name="tutorial-s3-cloudfront-route53-video-streaming"></a>

You can use Amazon S3 with Amazon CloudFront to host videos for on\-demand viewing with security and scalability\. Video on demand \(VOD\) streaming means your video content is stored on a server and viewers can watch it at any time\.

CloudFront is a fast, highly secure, and programmable content delivery network \(CDN\) service\. CloudFront can deliver your content securely over HTTPS from all of CloudFront's edge locations\. For more information about CloudFront, see the *[Amazon CloudFront Developer Guide](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html)*\.

CloudFront caching reduces the number of requests that your origin server must respond to directly\. When a viewer \(end user\) requests a video that you serve with CloudFront, the request is routed to a nearby edge location closer to where the viewer is located\. CloudFront serves the video from its cache, retrieving it from the S3 bucket only if it is not already cached\. This accelerates the delivery of your video to viewers globally with low latency, high throughput, and high transfer speeds\. For more information about CloudFront caching management, see [Optimizing caching and availability](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/ConfiguringCaching.html) in the *Amazon CloudFront Developer Guide*\.

![\[It describes the caching mechanism of CloudFront.\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/cf-example-image-global.png)

**Objective**  
In this tutorial, you configure an Amazon S3 bucket to host on\-demand video streaming using CloudFront for delivery and Amazon Route 53 for DNS and custom domain management\.

**Topics**
+ [Prerequisites: Register and configure a custom domain with Route 53](#cf-s3-prerequisites)
+ [Step 1: Create an S3 bucket](#cf-s3-step1)
+ [Step 2: Upload a video to the S3 bucket](#cf-s3-step2)
+ [Step 3: Create a CloudFront OAI](#cf-s3-step3)
+ [Step 4: Create a CloudFront distribution](#cf-s3-step4)
+ [Step 5: Access the S3 video through the CloudFront distribution](#cf-s3-step5)
+ [Step 6: Configure your CloudFront distribution to use the custom domain name](#cf-s3-step6)
+ [Step 7: Access the S3 video through the CloudFront distribution with the custom domain name](#cf-s3-step7)
+ [\(Optional\) Step 8: View data about requests received by your CloudFront distribution](#cf-s3-step8)
+ [Step 9: Clean up](#cf-s3-step9)
+ [Next steps](#cf-s3-next-steps)

## Prerequisites: Register and configure a custom domain with Route 53<a name="cf-s3-prerequisites"></a>

Before you start this tutorial, you need to register and configure a custom domain \(for example, **example\.com**\) with Route 53 in order to configure your CloudFront distribution to use a custom domain name later\. 

Without a custom domain name, your S3 video is publicly accessible and hosted through CloudFront at a URL that looks similar to the following: 

```
https://CloudFront distribution domain name/Path to an S3 video
```

For example, **https://d111111abcdef8\.cloudfront\.net/sample\.mp4**\.

After you configure your CloudFront distribution to use a custom domain name configured with Route 53, your S3 video is publicly accessible and hosted through CloudFront at a URL that looks similar to the following: 

```
https://CloudFront distribution alternate domain name/Path to an S3 video
```

For example, **https://www\.example\.com/sample\.mp4**\. A custom domain name is simpler and more intuitive for your viewers to use\.

**To register a custom domain with Route 53**  
For more information, see [Registering a new domain using Route 53](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/domain-register.html)\.

When you register a domain name with Route 53, Route 53 creates the hosted zone for you, which you will use later in this tutorial\.

## Step 1: Create an S3 bucket<a name="cf-s3-step1"></a>

You need to create a bucket to store the original video that you plan to stream\.

**To create a bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. Choose **Create bucket**\. 

   The **Create bucket** page opens\.

1. In **Bucket name**, enter a name for your bucket \(for example, **tutorial\-bucket**\)\. 

   Enter your own value for the bucket name\. For more information on naming buckets in Amazon S3, see [Bucket naming rules](bucketnamingrules.md)\.

1. In **Region**, choose the AWS Region where you want the bucket to reside\. 

   If possible, you should pick the Region location that will likely be closest to the majority of your viewers\. For more information on the bucket Region, see [Buckets overview](UsingBucket.md)\.

1. In **Block Public Access settings for this bucket**, keep the default settings \(Block *all *public access is enabled\)\. 

   Even with Block *all* public access enabled, viewers will still be able to access the uploaded video through CloudFront\. This is a major advantage of using CloudFront to host a video stored in S3\.

   We recommend that you keep all settings enabled unless you need to turn off one or more of them for your use case\. For more information about blocking public access, see [Blocking public access to your Amazon S3 storage](access-control-block-public-access.md)\.

1. Keep the remaining settings set to the defaults\. 

   For more information on additional bucket settings, see [Creating a bucket](create-bucket-overview.md)\.

1. Choose **Create bucket**\.

## Step 2: Upload a video to the S3 bucket<a name="cf-s3-step2"></a>

**To upload a file to the bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you created in [Step 1](#cf-s3-step1) \(for example, **tutorial\-bucket**\)\.

1. On the **Objects** tab for your bucket, choose **Upload**\.

1. Under **Files and folders**, choose **Add files**\.

1. Choose a file to upload, and then choose **Open**\.

   For example, you can upload a video file named `sample.mp4`\.

1. Choose **Upload**\.

When uploading a video to S3, you can also use [Amazon S3 Transfer Acceleration](http://aws.amazon.com/s3/transfer-acceleration) to configure fast and secure file transfers\. Transfer Acceleration can speed up video uploading to your S3 bucket for long\-distance transfer of larger videos\. For more information, see [Configuring fast, secure file transfers using Amazon S3 Transfer Acceleration](transfer-acceleration.md)\. 

## Step 3: Create a CloudFront OAI<a name="cf-s3-step3"></a>

To restrict direct access to the video from your S3 bucket, you need to create a special CloudFront user called an origin access identity \(OAI\) and associate it with your distribution later\. By using an OAI, you make sure that viewers get the benefits of CloudFront because they can't bypass CloudFront and get the video directly from the S3 bucket\. Only CloudFront can access the file in the S3 bucket\. 

**To create a CloudFront OAI**

1. Sign in to the AWS Management Console and open the CloudFront console at [https://console.aws.amazon.com/cloudfront/v3/home](https://console.aws.amazon.com/cloudfront/v3/home)\.

1. In the **Security** section of the left navigation pane, choose **Origin access identities**\.

1. Choose **Create origin access identity**\.

1. Enter a name \(for example, **S3\-OAI**\) as a new origin access identity\.

1. Choose **Create**\.

## Step 4: Create a CloudFront distribution<a name="cf-s3-step4"></a>

To use CloudFront to serve and distribute the video in your S3 bucket, you need to create a CloudFront distribution\. 

**Topics**
+ [Create a CloudFront distribution](#cf-s3-step4-create-cloudfront)
+ [Review the bucket policy](#cf-s3-step4-review-bucket-policy)

### Create a CloudFront distribution<a name="cf-s3-step4-create-cloudfront"></a>

1. Sign in to the AWS Management Console and open the CloudFront console at [https://console.aws.amazon.com/cloudfront/v3/home](https://console.aws.amazon.com/cloudfront/v3/home)\.

1. Choose **Create distribution**\.

1. Under **Origin**, for **Origin domain**, choose the domain name of your S3 origin, which starts with the name of the S3 bucket that you created in [Step 1](#cf-s3-step1) \(for example, **tutorial\-bucket**\)\.

1. For **S3 bucket access**, choose **Yes use OAI \(bucket can restrict access to only CloudFront\)**\.

1. Under **Origin access identity**, choose the existing origin access identity you created in [Step 3](#cf-s3-step3) \(for example, **S3\-OAI**\)\.

1. Under **Bucket policy**, choose **Yes, update the bucket policy**\. 

1. For **Default cache behavior**, in the **Viewer** section, under **Viewer protocol policy**, choose **Redirect HTTP to HTTPS**\. 

   This means that HTTP requests are automatically redirected to HTTPS to secure your website and protect your viewers’ data\. 

1. For other settings under **Default cache behaviors**, keep the default values\.

   Optionally, you can control how long your file stays in a CloudFront cache before CloudFront forwards another request to your origin\. Reducing the duration allows you to serve dynamic content\. Increasing the duration means that your users get better performance because your files are more likely to be served directly from the edge cache\. A longer duration also reduces the load on your origin\. For more information, see [Managing how long content stays in the cache \(expiration\)](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Expiration.html)\.

1. Keep the remaining settings set to the defaults\. 

   For more information about different settings options, see [Values That You Specify When You Create or Update a Distribution](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/distribution-web-values-specify.html)\. 

1. At the bottom of the page, choose **Create distribution**\. 

1. On the **General** tab for your CloudFront distribution, under **Details**, the value of the **Last modified** column for your distribution changes from **Deploying** to the time stamp when the distribution was last modified\. This typically takes a few minutes\. 

### Review the bucket policy<a name="cf-s3-step4-review-bucket-policy"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you used above as the origin of your CloudFront distribution \(for example, **tutorial\-bucket**\)\.

1. Choose **Permissions**\.

1. In the **Bucket policy** text box, confirm that you see a statement similar to the following: 

   ```
   {
       "Version": "2008-10-17",
       "Id": "PolicyForCloudFrontPrivateContent",
       "Statement": [
           {
               "Sid": "1",
               "Effect": "Allow",
               "Principal": {
                   "AWS": "arn:aws:iam::cloudfront:user/CloudFront Origin Access Identity EXXXXXXXXXXXXX"
               },
               "Action": "s3:GetObject",
               "Resource": "arn:aws:s3:::tutorial-bucket/*"
           }
       ]
   }
   ```

   This is the statement that your CloudFront distribution adds to your bucket policy when you choose **Yes, update the bucket policy** above\.

   This bucket policy means you successfully configured the CloudFront distribution to restrict access to the S3 bucket so that objects can be accessed only through your CloudFront distribution\. 

## Step 5: Access the S3 video through the CloudFront distribution<a name="cf-s3-step5"></a>

Now, CloudFront can serve the video stored in your S3 bucket\. To access your video through CloudFront, you need to combine your CloudFront distribution domain name with the path to the video in the S3 bucket\.

**To create a URL to the S3 video using the CloudFront distribution domain name**

1. Sign in to the AWS Management Console and open the CloudFront console at [https://console.aws.amazon.com/cloudfront/v3/home](https://console.aws.amazon.com/cloudfront/v3/home)\.

1. To get the distribution domain name, do the following:

   1. Under the **Origins** column, identify the CloudFront distribution by finding its origin name, which starts with the S3 bucket you created in [Step 1](#cf-s3-step1) \(for example, **tutorial\-bucket**\)\. 

   1. After finding the distribution from the list, widen the **Domain name** column to copy the value of the **Domain name** for your CloudFront distribution\.

1. In a new browser tab, paste the distribution domain name that you copied above\. 

1. Return to the previous browser tab, and open the S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\. 

1. In the **Buckets** list, choose the name of the bucket that you created in [Step 1](#cf-s3-step1) \(for example, **tutorial\-bucket**\) \. 

1. In the **Objects** list, choose the name of the video you uploaded in [Step 2](#cf-s3-step2) for the purpose of streaming \(for example, `sample.mp4`\)\. 

1. On the **Object overview** page, choose to copy the value of the **Key** which is the path to the uploaded video object in the S3 bucket\. 

1. Return to the browser tab where you previously pasted the distribution domain name, continue typing **/**, and paste the path to the video that you copied above \(for example, `sample.mp4`\)\. 

   Now, your S3 video is publicly accessible and hosted through CloudFront at a URL that looks similar to the following: 

   ```
   https://CloudFront distribution domain name/Path to the S3 video
   ```

   You need to replace **CloudFront distribution domain name** and **Path to the S3 video** with the appropriate values\. An example URL is **https://d111111abcdef8\.cloudfront\.net/sample\.mp4**\.

## Step 6: Configure your CloudFront distribution to use the custom domain name<a name="cf-s3-step6"></a>

To use your own domain name instead of the CloudFront domain name in the URL to access the S3 video, you need to add an alternate domain name to your CloudFront distribution\. 

**Topics**
+ [Request a SSL certificate](#cf-s3-step6-create-SSL)
+ [Add the alternate domain name to your CloudFront distribution](#cf-s3-step6-custom-domain)
+ [Create a DNS record to route traffic of your alternate domain name to your CloudFront distribution’s domain name](#cf-s3-step6-DNS-record)
+ [Check if IPv6 is enabled for your distribution and create another DNS record if needed](#s3-step6-ipv6)

### Request a SSL certificate<a name="cf-s3-step6-create-SSL"></a>

To allow your viewers to use HTTPS and your custom domain name in the URL for your video streaming, you need to request a SSL certificate to establish an encrypted network connection to the web site\. 

1. Sign in to the AWS Management Console and open the ACM console at [https://console\.aws\.amazon\.com/acm/](https://console.aws.amazon.com/acm/)\.

1. If the introductory page appears, under **Provision certificates**, choose **Get Started**\.

1. On the **Request a certificate** page, choose **Request a public certificate**, and then **Request a certificate** to continue\.

1. On the **Add domain names** page, type the fully qualified domain name of the site you want to secure with an SSL/TLS certificate\. You can use an asterisk \(\*\) to request a wild\-card certificate to protect several site names in the same domain\. Specifically, in this tutorial, type **\*** and the custom domain name you configured in Prerequisites\. For example, type **\*\.example\.com** and choose **Next**\. 

   For more information, see [To request an ACM public certificate \(console\)](https://docs.aws.amazon.com/acm/latest/userguide/gs-acm-request-public.html#request-public-console) in the *AWS Certificate Manager User Guide*\.

1. On the **Select validation method** page, choose **DNS validation**\. Then, choose **Next**\. 

   If you are able to edit your DNS configuration, we recommend that you use DNS domain validation rather than email validation\. DNS validation has multiple benefits over email validation\. See [Option 1: DNS validation](https://docs.aws.amazon.com/acm/latest/userguide/dns-validation.html)\. 

1. \(Optional\) On the **Add tags** page, you can tag your certificate with metadata\.

1. Choose **Review**\. 

1. On the **Review** page, verify that information under **Domain name** and **Validation method** are correct\. Then, choose **Confirm and request**\. 

   The **Validation** page shows that your request is being processed and the certificate domain is being validated\. The certificate awaiting validation is in the **Pending validation** status\. 

1. On the **Validation** page, choose the down\-arrow on the left of your custom domain name, and choose **Create record in Route 53** to validate your domain ownership through DNS\.

   This adds a CNAME record provided by AWS Certificate Manager to your DNS configuration\.

1. In the **Create record in Route 53** dialog box, choose **Create**\.

   The **Validation** page now should display a status notification of **Success** at the bottom\.

1. Choose **Continue** to view the **Certificates** list page\. 

   The **Status** for your new certificate will change from **Pending validation** to **Issued** within 30 minutes\.

### Add the alternate domain name to your CloudFront distribution<a name="cf-s3-step6-custom-domain"></a>

1. Sign in to the AWS Management Console and open the CloudFront console at [https://console.aws.amazon.com/cloudfront/v3/home](https://console.aws.amazon.com/cloudfront/v3/home)\.

1. Choose the ID for the distribution that you created in [Step 4](#cf-s3-step3)\.

1. On the **General** tab, choose **Edit**\.

1. For **Alternate domain name \(CNAME\) \- optional**, choose **Add item** to add the custom domain names that you use in URLs for the S3 video served by this CloudFront distribution\.

   In this tutorial, for example, if you want to route traffic for a subdomain, such as `www.example.com`, enter the subdomain name with the domain name\. Specifically, enter **www\.example\.com**\. 
**Note**  
The alternate domain name \(CNAME\) that you add must be covered by the SSL certificate that you previously attached to your CloudFront distribution\.

1. For **Custom SSL certificate \- optional**, choose the SSL certificate that you requested above \(for example, **\*\.example\.com**\)\.
**Note**  
If you don't see the SSL certificate immediately after you request it, you can wait and then refresh the list until the SSL certificate is available for you to select\.

1. Keep the remaining settings set to the defaults\. Choose **Save changes**\. 

1. On the **General** tab for the distribution, wait for the value of **Last modified** to change from **Deploying** to the time stamp when the distribution was last modified\. 

### Create a DNS record to route traffic of your alternate domain name to your CloudFront distribution’s domain name<a name="cf-s3-step6-DNS-record"></a>

1. Sign in to the AWS Management Console and open the Route 53 console at [https://console\.aws\.amazon\.com/route53/](https://console.aws.amazon.com/route53/)\.

1. In the navigation pane, choose **Hosted zones**\.

1. On the **Hosted zones** page, choose the name of the hosted zone that Route 53 created for you in Prerequisites \(for example, **example\.com**\)\.

1. Choose **Create record**\. 

1. For **Record name**, keep the value for the record name the same as the alternate domain name of the CloudFront distribution that you added above\.

   In this tutorial, to route traffic to a subdomain, such as `www.example.com`, enter the subdomain name without the domain name\. For example, enter only **www** in the text field before your custom domain name\.

1. For **Record type**, choose **A \- Routes traffic to an IPv4 address and some AWS resources**\.

1. For **Value**, to enable the alias resource, choose the **Alias** toggle\. 

1. Under **Route traffic to**, in the field of **Choose endpoint**, choose **Alias to CloudFront distribution**\. 

1. In the field of **Choose distribution**, choose the domain name of the CloudFront distribution that you created in [Step 4](#cf-s3-step4)\. 

   To find the domain name of your CloudFront distribution, do the following:

   1. In a new browser tab, sign in to the AWS Management Console and open the CloudFront console at [https://console.aws.amazon.com/cloudfront/v3/home](https://console.aws.amazon.com/cloudfront/v3/home)\.

   1. Under the **Origins** column, identify the CloudFront distribution by finding its origin name which starts with the S3 bucket you created in [Step 1](#cf-s3-step1) \(for example, **tutorial\-bucket**\)\.

   1. After finding the distribution from the list, widen the **Domain name** column to find the value of the **Domain name** for your CloudFront distribution\. 

1. Keep the remaining settings set to the defaults\. 

1. Choose **Create records**\.

### Check if IPv6 is enabled for your distribution and create another DNS record if needed<a name="s3-step6-ipv6"></a>

If IPv6 is enabled for your distribution, you need to create another DNS record\. 

1. To check whether IPv6 is enabled for your distribution, do the following:

   1. Sign in to the AWS Management Console and open the CloudFront console at [https://console.aws.amazon.com/cloudfront/v3/home](https://console.aws.amazon.com/cloudfront/v3/home)\.

   1. Choose the CloudFront distribution you created in [Step 4](#cf-s3-step4)\.

   1. On the **General** tab, under **Settings**, check whether **IPv6** is **Enabled**\. 

      If IPv6 is enabled for your distribution, you need to create another DNS record\.

1. If IPv6 is enabled for your distribution, do the following to create a DNS record:

   1. Sign in to the AWS Management Console and open the Route 53 console at [https://console\.aws\.amazon\.com/route53/](https://console.aws.amazon.com/route53/)\.

   1. In the navigation pane, choose **Hosted zones**\.

   1. On the **Hosted zones** page, choose the name of the hosted zone that Route 53 created for you in Prerequisites \(for example, **example\.com**\)\.

   1. Choose **Create record**\.

   1. For **Record name**, in the text field before your custom domain name, type the same value that you typed when you created the IPv4 DNS record above\. For example, in this tutorial, to route traffic for `www.example.com`, enter only **www**\. 

   1. For **Record type**, choose **AAAA \- Routes traffic to an IPv6 address and some AWS resources**\. 

   1. For **Value**, choose the **Alias** toggle to enable Alias resource\. 

   1. Under **Route traffic to**, in the field of **Choose endpoint**, choose **Alias to CloudFront distribution**\. 

   1. In the field of **Choose distribution**, choose the domain name of the CloudFront distribution that you created in [Step 4](#cf-s3-step4)\. 

   1. Keep the remaining settings set to the defaults\. 

   1. Choose **Create records**\.

## Step 7: Access the S3 video through the CloudFront distribution with the custom domain name<a name="cf-s3-step7"></a>

To access the S3 video using the custom URL, you need to combine your alternate domain name with the path to the video in the S3 bucket\. 

**To create a custom URL to access the S3 video through the CloudFront distribution**

1. Sign in to the AWS Management Console and open the CloudFront console at [https://console.aws.amazon.com/cloudfront/v3/home](https://console.aws.amazon.com/cloudfront/v3/home)\.

1. To get the alternate domain name of your CloudFront distribution, do the following:

   1. Under the **Origins** column, identify the CloudFront distribution by finding its origin name, which starts with the S3 bucket name for the bucket you created in [Step 1](#cf-s3-step1) \(for example, **tutorial\-bucket**\)\. 

   1. After finding the distribution from the list, widen the **Alternate domain names** column to copy the value of the **Alternate domain names** of your CloudFront distribution\.

1. In a new browser tab, paste the alternate domain name of the CloudFront distribution that you copied above\. 

1. Return to the previous browser tab, and open the S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\. 

1. Find the path to your S3 video explained in [Step 5](#cf-s3-step5)\. 

1. Return to the browser tab where you previously pasted the alternate domain name, continue typing **/**, and paste the path to your S3 video \(for example, `sample.mp4`\)\. 

   Now, your S3 video is publicly accessible and hosted through CloudFront at a custom URL that looks similar to the following: 

   ```
   https://CloudFront distribution alternate domain name/Path to the S3 video
   ```

   You need to replace **CloudFront distribution alternate domain name** and **Path to the S3 video** with the appropriate values\. An example URL is **https://www\.example\.com/sample\.mp4**\.

## \(Optional\) Step 8: View data about requests received by your CloudFront distribution<a name="cf-s3-step8"></a>

**To view data about requests received by your CloudFront distribution**

1. Sign in to the AWS Management Console and open the CloudFront console at [https://console.aws.amazon.com/cloudfront/v3/home](https://console.aws.amazon.com/cloudfront/v3/home)\.

1. In the left pane of the CloudFront console, under **Reports & analytics**, choose the reports from the console, ranging from **Cache statistics**, **Popular Objects**, **Top Referrers**, **Usage**, and **Viewers**\. 

   You can filter each report dashboard\. For more information, see [CloudFront Reports in the Console](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/reports.html)\. 

1. To filter data, choose the ID of the CloudFront distribution that you created in [Step 4](#cf-s3-step4)\.

## Step 9: Clean up<a name="cf-s3-step9"></a>

If you hosted the an S3 steaming video using CloudFront and Route 53 only as a learning exercise, delete the AWS resources that you allocated so that you no longer accrue charges\.

**Note**  
When you register a domain, it costs money immediately and it's irreversible\. You can choose not to auto\-renew the domain, but you paid up front and own it for the year\)\. For more information, see [Registering a new domain](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/domain-register.html)\. 

**Topics**
+ [Delete the CloudFront distribution](#cf-s3-step9-delete-cf)
+ [Delete the DNS record](#cf-s3-step9-delete-dns)
+ [Delete the public hosted zone for your custom domain](#cf-s3-step9-delete-hosted-zone)
+ [Delete the custom domain name from Route 53](#cf-s3-step9-delete-domain)
+ [Delete the original video in the S3 source bucket](#cf-s3-step9-delete-video)
+ [Delete the S3 source bucket](#cf-s3-step9-delete-bucket)

### Delete the CloudFront distribution<a name="cf-s3-step9-delete-cf"></a>

1. Sign in to the AWS Management Console and open the CloudFront console at [https://console.aws.amazon.com/cloudfront/v3/home](https://console.aws.amazon.com/cloudfront/v3/home)\.

1. In the right pane of the CloudFront console, find the distribution that you created in [Step 4](#cf-s3-step4)\)\.

1. To delete the CloudFront distribution, you must disable it first\.
   + If the value of the **Status** column is **Enabled** and the value of **Last modified** is the time stamp when the distribution was last modified, continue to disable the distribution before deleting it\.
   + If the value of **Status** is **Enabled** and the value of **Last modified** is **Deploying**, wait until **Status** changes to the time stamp when the distribution was last modified\. Then continue to disable the distribution before deleting it\.

1. To disable the CloudFront distribution, do the following:

   1. In the right pane of the CloudFront console, select the check box for the distribution that you want to delete\. 

   1. To disable the distribution, choose **Disable**, and choose **Disable** to confirm\.

      If you disable a distribution that has an alternate domain name associated with it, CloudFront stops accepting traffic for that domain name \(such as `www.example.com`\), even if another distribution has an alternate domain name with a wildcard \(`*`\) that matches the same domain \(such as `*.example.com`\)\.

   1. The value of **Status** immediately changes to **Disabled**\. Wait until the value of **Last modified** changes from **Deploying** to the time stamp when the distribution was last modified\. 

      Because CloudFront must propagate this change to all edge locations, it might take a few minutes before the update is complete and the **Delete** option is available for you to delete the distribution\. 

1. To delete the disabled distribution, do the following:

   1. Choose the check box for the distribution that you want to delete\.

   1. Choose **Delete**, and choose **Delete** to confirm\.

### Delete the DNS record<a name="cf-s3-step9-delete-dns"></a>

If you want to delete the public hosted zone for the domain \(including the DNS record\), go to [Delete the public hosted zone for your custom domain](#cf-s3-step9-delete-hosted-zone)\. If you only want to delete the DNS record created in [Step 6](#cf-s3-step6)\), do the following:

1. Sign in to the AWS Management Console and open the Route 53 console at [https://console\.aws\.amazon\.com/route53/](https://console.aws.amazon.com/route53/)\.

1. In the navigation pane, choose **Hosted zones**\.

1. On the **Hosted zones** page, choose the row for the hosted zone that Route 53 created for you in Prerequisites that contains the records you want to delete \(for example, **example\.com**\)\.

1. In the list of records, select the records that you want to delete \(the records you created in [Step 6](#cf-s3-step6)\)\. 

   You can't delete the records that have a value of **NS** or **SOA** for **Type**\. 

1. Choose **Delete records**\. 

1. To close the dialog box, choose **Delete**\.

   Your changes to records take time to propagate to the Route 53 DNS servers\. Currently, the only way to verify that changes have propagated is to use the [GetChange API action](https://docs.aws.amazon.com/Route53/latest/APIReference/API_GetChange.html)\. Changes generally propagate to all Route 53 name servers within 60 seconds\.

### Delete the public hosted zone for your custom domain<a name="cf-s3-step9-delete-hosted-zone"></a>

If you want to keep your domain registration but stop routing internet traffic to your website or web application, we recommend that you delete records in the hosted zone \(as above\) instead of deleting the hosted zone\. If you delete a hosted zone, you can't undelete it\. You must create a new hosted zone and update the name servers for your domain registration, which can take up to 48 hours to take effect\. In addition, if you delete a hosted zone, someone can use the domain and route traffic to their own resources using your domain name\. 



1. Sign in to the AWS Management Console and open the Route 53 console at [https://console\.aws\.amazon\.com/route53/](https://console.aws.amazon.com/route53/)\.

1. In the navigation pane, choose **Hosted zones**\.

1. On the **Hosted zones** page, choose the name of the hosted zone that you want to delete\.

1. On the **Records** tab for your hosted zone, confirm that the hosted zone that you want to delete contains only an **NS** and an **SOA** record\.

   If it contains additional records, delete them\.

   If you created any NS records for subdomains in the hosted zone, delete those records too\.

1. On the **DNSSEC signing** tab for your hosted zone, disable DNNSSEC signing if it was enabled\. For more information, see [Disabling DNSSEC signing](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-configuring-dnssec-disable.html)\.

1. On the page of the hosted zone, choose **Delete zone**\.

1. Type **delete** and choose **Delete**\.

1. If you want to make the domain unavailable on the internet, you can first transfer DNS service to a free DNS service and then delete the Route 53 hosted zone\. This prevents future DNS queries from possibly being misrouted\. 

   1. If the domain is registered with Route 53, see [Adding or changing name servers and glue records for a domain](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/domain-name-servers-glue-records.html) for information about how to replace Route 53 name servers with name servers for the new DNS service\. 

   1. If the domain is registered with another registrar, use the method provided by the registrar to change name servers for the domain\. 
**Note**  
If you're deleting a hosted zone for a subdomain \(`acme.example.com`\), you don't need to change name servers for the domain \(`example.com`\)\.

### Delete the custom domain name from Route 53<a name="cf-s3-step9-delete-domain"></a>

For most top\-level domains \(TLDs\), you can delete the registration if you no longer want it\. If you delete a domain name registration from Route 53 before the registration is scheduled to expire, AWS does not refund the registration fee\. If you just want to transfer the domain between AWS accounts or transfer the domain to another registrar, don't delete a domain and expect to re\-register it\. For more information, see [To delete a domain name registration](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/domain-delete.html) in the *Amazon Route 53 Developer Guide*\.

### Delete the original video in the S3 source bucket<a name="cf-s3-step9-delete-video"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Bucket name** list, choose the name of the bucket that you uploaded the original video to in [Step 2](#cf-s3-step2) \(for example, **tutorial\-bucket**\)\.

1. Choose the name of the object that you want to delete \(for example, `sample.mp4`\)\.

1. Choose **Delete**\. 

1. To confirm that you want to delete this object, type **permanently delete**\.

1. Choose **Delete objects**\.

### Delete the S3 source bucket<a name="cf-s3-step9-delete-bucket"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the radio button next to the name of the bucket that you created in [Step 1](#cf-s3-step1) \(for example, **tutorial\-bucket**\)\.

1. Choose **Delete**\.

1. On the **Delete bucket** page, confirm that you want to delete the bucket by entering the bucket name into the text field, and then choose **Delete bucket**\.

## Next steps<a name="cf-s3-next-steps"></a>

After you complete this tutorial, you can try the following related use cases:
+ Transcode S3 videos into streaming formats needed by a particular television or connected device before hosting these videos with a CloudFront distribution\.

  To use Amazon S3 Batch Operations, AWS Lambda and AWS Elemental MediaConvert to batch\-transcode a collection of videos to a variety of output media formats, see [Tutorial: Batch\-transcoding videos with S3 Batch Operations, Lambda, and MediaConvert](tutorial-s3-batchops-lambda-mediaconvert-video.md) t\. 
+ Host other objects stored in S3, such as images, audio, motion graphics, style sheets, HTML, JavaScript, React apps, etc\., using CloudFront and Route 53\.

  For example, see [Configuring a static website using a custom domain registered with Route 53](website-hosting-custom-domain-walkthrough.md) and [Speeding up your website with Amazon CloudFront](website-hosting-cloudfront-walkthrough.md)\. 
+ Use [Transfer Acceleration](http://aws.amazon.com/s3/transfer-acceleration) to configure fast and secure file transfers\. It can speed up video uploading to your S3 bucket for long\-distance transfer of larger videos\. [Transfer Acceleration](http://aws.amazon.com/s3/transfer-acceleration) improves transfer performance by routing traffic through CloudFront’s globally distributed Edge locations and over AWS backbone networks\. It also utilizes network protocol optimizations\. For more information, see [Configuring fast, secure file transfers using Amazon S3 Transfer Acceleration](transfer-acceleration.md)\. 