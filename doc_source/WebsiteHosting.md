# Hosting a static website using Amazon S3<a name="WebsiteHosting"></a>

You can use Amazon S3 to host a static website\. On a *static* website, individual webpages include static content\. They might also contain client\-side scripts\.

By contrast, a *dynamic* website relies on server\-side processing, including server\-side scripts, such as PHP, JSP, or ASP\.NET\. Amazon S3 does not support server\-side scripting, but AWS has other resources for hosting dynamic websites\. To learn more about website hosting on AWS, see [Web Hosting](https://aws.amazon.com/websites/)\. 

**Note**  
You can use the AWS Amplify Console to host a single\-page web app\. The AWS Amplify Console supports single\-page apps built with single\-page app frameworks \(for example, React JS, Vue JS, Angular JS, and Nuxt\) and static site generators \(for example, Gatsby JS, React\-static, Jekyll, and Hugo\)\. For more information, see [Getting Started](https://docs.aws.amazon.com/amplify/latest/userguide/getting-started.html) in the *AWS Amplify Console User Guide*\.  
Amazon S3 website endpoints do not support HTTPS\. If you want to use HTTPS, you can use Amazon CloudFront to serve a static website hosted on Amazon S3\. For more information, see [How do I use CloudFront to serve HTTPS requests for my Amazon S3 bucket?](https://aws.amazon.com/premiumsupport/knowledge-center/cloudfront-https-requests-s3) To use HTTPS with a custom domain, see [Configuring a static website using a custom domain registered with Route 53](https://docs.aws.amazon.com/AmazonS3/latest/userguide/website-hosting-custom-domain-walkthrough.html)\.

For more information about hosting a static website on Amazon S3, including instructions and step\-by\-step walkthroughs, see the following topics\.

**Topics**
+ [Website endpoints](WebsiteEndpoints.md)
+ [Enabling website hosting](EnableWebsiteHosting.md)
+ [Configuring an index document](IndexDocumentSupport.md)
+ [Configuring a custom error document](CustomErrorDocSupport.md)
+ [Setting permissions for website access](WebsiteAccessPermissionsReqd.md)
+ [\(Optional\) Logging web traffic](LoggingWebsiteTraffic.md)
+ [\(Optional\) Configuring a webpage redirect](how-to-page-redirect.md)