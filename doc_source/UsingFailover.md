# Using Amazon S3 Multi\-Region Access Point failover controls<a name="UsingFailover"></a>

This section explains how to manage and use your Amazon S3 Multi\-Region Access Points failover controls by using the AWS Management Console\. 

There are two failover controls in the **Failover configuration** section on your Multi\-Region Access Point details page in the AWS Management Console: **Edit routing status** and **Failover**\. You can use these controls as follows: 
+ **Edit routing status** – You can manually edit the routing statuses of up to 17 AWS Regions in a single request for your Multi\-Region Access Point by choosing **Edit routing status**\. You can use **Edit routing status** for the following purposes: 
  + To set or edit the routing statuses of one or more Regions in your Multi\-Region Access Point
  + To create a failover configuration for your Multi\-Region Access Point by configuring two Regions to be in an active\-passive state
  + To manually fail over your Regions
  + To manually switch traffic between Regions
+ **Failover** – When you initiate failover by choosing **Failover**, you are only updating the routing statuses of two Regions that are already configured to be in an active\-passive state\. During a failover that you initiated by choosing **Failover**, the routing statuses between the two Regions are automatically switched\.

## Editing the routing status of the Regions in your Multi\-Region Access Point<a name="updating-mrap-routing-statuses"></a>

You can manually update the routing statuses of up to 17 AWS Regions in a single request for your Multi\-Region Access Point by choosing **Edit routing status** in the **Failover configuration** section on your Multi\-Region Access Point details page\. However, when you initiate failover by choosing **Failover**, you are only updating the routing statuses of two Regions that are already configured to be in an active\-passive state\. During a failover that you initiated by choosing **Failover**, the routing statuses between the two Regions are automatically switched\.

You can use **Edit routing status** \(as described in the following procedure\) for the following purposes: 
+ To set or edit the routing statuses of one or more Regions in your Multi\-Region Access Point
+ To create a failover configuration for your Multi\-Region Access Point by configuring two Regions to be in an active\-passive state
+ To manually fail over your Regions
+ To manually switch traffic between Regions

## Initiating failover<a name="InitiatingFailover"></a>

When you initiate failover by choosing **Failover** in the **Failover configuration** section on your Multi\-Region Access Point details page, Amazon S3 request traffic automatically gets shifted to an alternate AWS Region\. The failover process is completed within 2 minutes\. 

You can initiate a failover across any two AWS Regions at one time \(of the [17 Regions](https://docs.aws.amazon.com/AmazonS3/latest/userguide/MultiRegionAccessPointRestrictions.html) where Multi\-Region Access Points are supported\)\. Failover events are then logged in AWS CloudTrail\. Upon failover completion, you can monitor Amazon S3 traffic and any traffic routing updates to the new active Region in Amazon CloudWatch\.

**Important**  
To keep all metadata and objects in sync across buckets during data replication, we recommend that you create two\-way replication rules and enable replica modification sync before configuring your failover controls\.   
Two\-way replication rules help ensure that when data is written to the Amazon S3 bucket that traffic fails over to, that data is then replicated back to the source bucket\. Replica modification sync helps ensure that object metadata is also synchronized between buckets during two\-way replication\.   
For more information about configuring replication to support failover, see [Configuring bucket replication for use with Multi\-Region Access Points](MultiRegionAccessPointBucketReplication.md)\.

**To initiate failover between replicated buckets**

1. Sign in to the AWS Management Console\.

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Multi\-Region Access Points**\.

1. Choose the Multi\-Region Access Point that you want to use to initiate failover\.

1. Choose the **Replication and failover** tab\.

1. Scroll down to the **Failover configuration** section and select two AWS Regions\.
**Note**  
To initiate failover, at least one AWS Region must be designated as **Active** and one Region must be designated as **Passive** in your Multi\-Region Access Point\. An active state allows traffic to be directed to a Region\. A passive state stops any traffic from being directed to the Region\.

1. Choose **Failover**\.

1. In the dialog box, choose **Failover** again to initiate the failover process\. During this process, the routing statuses of the two Regions are automatically switched\. All new traffic is directed to the Region that becomes active, and traffic stops being directed to the Region that becomes passive\. It takes about 2 minutes for traffic to be redirected\.

   After you initiate the failover process, you can verify your traffic changes\. To verify these changes, go to Amazon CloudWatch at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/) to monitor the shift of your Amazon S3 data\-request traffic \(for example, `GET` and `PUT` requests\) between active and passive Regions\. Any existing connections will not be terminated during failover\. Existing connections will continue until they reach a success or failure status\. 

## Viewing your Amazon S3 Multi\-Region Access Point routing controls<a name="viewing-mrap-routing-controls"></a>

### Using the S3 console<a name="viewing-mrap-routing-console"></a>

**To view the routing controls for your Amazon S3 Multi\-Region Access Point**



1. Sign in to the AWS Management Console\.

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Multi\-Region Access Points**\.

1. Choose the Multi\-Region Access Point that you want to review\.

1. Choose the **Replication and failover** tab\. This page displays the routing configuration details and summary for your Multi\-Region Access Point, associated replication rules, and replication metrics\. You can see the routing status of your Regions in the **Failover configuration** section\.

### Using the AWS CLI<a name="viewing-mrap-routing-cli"></a>

The following example AWS CLI command gets your current Multi\-Region Access Point route configuration for the specified Region\. To use this example command, replace the `user input placeholders` with your own information\.

```
aws s3control get-multi-region-access-point-routes
--region eu-west-1
--account-id 111122223333 
--mrap MultiRegionAccessPoint_ARN
```

**Note**  
This command can only be executed against these five Regions:  
`ap-southeast-2`
`ap-northeast-1`
`us-east-1`
`us-west-2`
`eu-west-1`

## Editing the routing status of the Regions in your Multi\-Region Access Point<a name="editing-mrap-routing-status"></a>

You can manually update the routing statuses of up to 17 AWS Regions in a single request for your Multi\-Region Access Point by choosing **Edit routing status** in the **Failover configuration** section on your Multi\-Region Access Point details page\. However, when you initiate failover by choosing **Failover**, you are only updating the routing statuses of two Regions that are already configured to be in an active\-passive state\. During a failover that you initiated by choosing **Failover**, the routing statuses between the two Regions are automatically switched\.

You can use **Edit routing status** \(as described in the following procedure\) for the following purposes:
+ To set or edit the routing statuses of one or more Regions in your Multi\-Region Access Point
+ To create a failover configuration for your Multi\-Region Access Point by configuring two Regions to be in an active\-passive state
+ To manually fail over your Regions
+ To manually switch traffic between Regions

### Using the S3 console<a name="update-mrap-routing-console"></a>

**To update the routing status of the Regions in your Multi\-Region Access Point**



1. Sign in to the AWS Management Console\.

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Multi\-Region Access Points**\.

1. Choose the Multi\-Region Access Point that you want to update\.

1. Choose the **Replication and failover** tab\.

1. Select one or more Regions that you want to edit the routing status of\.
**Note**  
To initiate failover, at least one AWS Region must be designated as **Active** and one Region must be designated as **Passive** in your Multi\-Region Access Point\.

1. Choose **Edit routing status**\.

1. In the dialog box that appears, select **Active** or **Passive** for the **Routing status** for each Region\.

   An active state allows traffic to be routed to the Region\. A passive state stops any traffic from being directed to the Region\.

   If you are creating a failover configuration for your Multi\-Region Access Point or initiating failover, at least one AWS Region must be designated as **Active** and one Region must be designated as **Passive** in your Multi\-Region Access Point\.

1. Choose **Save routing status**\. It takes about 2 minutes for traffic to be redirected\.

After you submit the routing status of the AWS Regions for your Multi\-Region Access Point, you can verify your routing status changes\. To verify these changes, go to Amazon CloudWatch at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/) to monitor the shift of your Amazon S3 data\-request traffic \(for example, `GET` and `PUT` requests\) between active and passive Regions\. Any existing connections will not be terminated during failover\. Existing connections will continue until they reach a success or failure status\.

### Using the AWS CLI<a name="update-mrap-routing-cli"></a>

**Note**  
You can run Multi\-Region Access Point AWS CLI routing commands against any of these five Regions:  
`ap-southeast-2`
`ap-northeast-1`
`us-east-1`
`us-west-2`
`eu-west-1`

The following example command updates your current Multi\-Region Access Point route configuration\. To update the active or passive status of a bucket, set the `TrafficDialPercentage` value to `100` for active and to `0` for passive\. In this example, `DOC-EXAMPLE-BUCKET-1` is set to active, and `DOC-EXAMPLE-BUCKET-2` is set to passive\. To use this example command, replace the `user input placeholders` with your own information\. 

```
aws s3control submit-multi-region-access-point-routes
--region ap-southeast-2 
--account-id 111122223333 
--mrap MultiRegionAccessPoint_ARN
--route-updates Bucket=DOC-EXAMPLE-BUCKET-1,TrafficDialPercentage=100
                Bucket=DOC-EXAMPLE-BUCKET-2,TrafficDialPercentage=0
```

The following example command gets your updated Multi\-Region Access Point routing configuration\. To use this example command, replace the `user input placeholders` with your own information\.

```
aws s3control get-multi-region-access-point-routes
--region eu-west-1
--account-id 111122223333
--mrap MultiRegionAccessPoint_ARN
```