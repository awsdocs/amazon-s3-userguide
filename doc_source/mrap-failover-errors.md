# Amazon S3 Multi\-Region Access Point failover controls errors<a name="mrap-failover-errors"></a>

When you update the failover configuration for your Multi\-Region Access Point, you might encounter one of these errors:
+ HTTP 400 Bad Request: This occur can occur if you enter an invalid Multi\-Region Access PointARN while updating your failover configuration\. You can confirm your Multi\-Region Access Point ARN by reviewing your Multi\-Region Access Point policy\. To review or update your Multi\-Region Access Point policy, see [Editing the Multi\-Region Access Point policy](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingFailover.html#editing-mrap-policy)\. This error can also occur if you use an empty string or a random string while updating your Amazon S3 Multi\-Region Access Point failover controls\. Make sure to use the Multi\-Region Access Point ARN format: 

  `arn:aws:s3::account-id:accesspoint/MultiRegionAccessPoint_alias` 
+ HTTP 503 Slow Down: This error occurs if you send too many requests in a short period of time\. Rejected requests will result in an error\.
+ HTTP 409 Conflict: This error occurs when two or more concurrent route configuration update requests are targeting a single Multi\-Region Access Point\. The first request succeeds, but any other requests fail with an error\.
+ HTTP 405 Method Not Allowed: This error occurs when you've selected a Multi\-Region Access Point with only one AWS Region when initiating failover\. You must select two Regions before you can initiate failover\. Otherwise, an error is returned\.