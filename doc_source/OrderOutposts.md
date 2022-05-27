# Setting up your Outpost<a name="OrderOutposts"></a>

To get started with Amazon S3 on Outposts, you will need an Outpost with Amazon S3 capacity deployed at your facility\. For information about options for ordering an Outpost and S3 capacity, see [AWS Outposts](http://aws.amazon.com/outposts)\. For specifications and to see how S3 on Outposts is different than Amazon S3, see [How is Amazon S3 on Outposts different from Amazon S3?](S3OnOutpostsRestrictionsLimitations.md)

For more information, see the following topics\.

**Topics**
+ [Order a new Outpost](#SettingUpS3OutpostsNewOutpost)
+ [Add Amazon S3 storage to an existing Outpost](#SettingUpS3OutpostsExistingOutpost)

## Order a new Outpost<a name="SettingUpS3OutpostsNewOutpost"></a>

If you need to order a new Outpost with S3 capacity, see [AWS Outposts rack pricing](http://aws.amazon.com/outposts/pricing/) to understand the capacity options for Amazon Elastic Compute Cloud \(Amazon EC2\), Amazon Elastic Block Store \(Amazon EBS\), and Amazon S3\. 

After you select your configuration, follow the steps in [Create an Outpost and order Outpost capacity](https://docs.aws.amazon.com/outposts/latest/userguide/order-outpost-capacity.html) in the *AWS Outposts User Guide\.* 

## Add Amazon S3 storage to an existing Outpost<a name="SettingUpS3OutpostsExistingOutpost"></a>

If AWS Outposts is already deployed at your site, depending on your current Outpost configuration and storage capacity, you might be able to add Amazon S3 storage to an existing Outpost, or you might need to work with your AWS account team to add additional hardware to support Amazon S3 on Outposts\.

**To add Amazon S3 capacity to an existing Outpost**

1. From the AWS Outposts owner account, sign in to the AWS Management Console and open the AWS Outposts console at [https://console\.aws\.amazon\.com/outposts/](https://console.aws.amazon.com/outposts/)\.

1. Choose the Outpost ID that you would like to add capacity to\.

1. Choose **Actions**, and then choose **Increase capacity**\.

1. Choose **Request increase**\.
**Note**  
An AWS representative will contact you within three business days\. The increase in capacity might require AWS to install additional equipment at your site\.