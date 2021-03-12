--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Using the Amazon S3 Transfer Acceleration Speed Comparison tool<a name="transfer-acceleration-speed-comparison"></a>

You can use the [Amazon S3 Transfer Acceleration Speed Comparison tool](https://s3-accelerate-speedtest.s3-accelerate.amazonaws.com/en/accelerate-speed-comparsion.html) to compare accelerated and non\-accelerated upload speeds across Amazon S3 Regions\. The Speed Comparison tool uses multipart uploads to transfer a file from your browser to various Amazon S3 Regions with and without using Transfer Acceleration\.

You can access the Speed Comparison tool using either of the following methods:
+ Copy the following URL into your browser window, replacing *region* with the AWS Region that you are using \(for example, `us-west-2`\) and *yourBucketName* with the name of the bucket that you want to evaluate: 

  `https://s3-accelerate-speedtest.s3-accelerate.amazonaws.com/en/accelerate-speed-comparsion.html?region=region&origBucketName=yourBucketName` 

  For a list of the Regions supported by Amazon S3, see [Amazon S3 endpoints and quotas](https://docs.aws.amazon.com/general/latest/gr/s3.html) in the *AWS General Reference*\.
+ Use the Amazon S3 console\. 