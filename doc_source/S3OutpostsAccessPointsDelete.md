# Deleting an access point<a name="S3OutpostsAccessPointsDelete"></a>

Access points simplify managing data access at scale for shared datasets in Amazon S3\. Access points are named network endpoints that are attached to buckets that you can use to perform Amazon S3 object operations, such as `GetObject` and `PutObject`\. With S3 on Outposts, you must use access points to access any object in an Outposts bucket\. Access points support only virtual\-host\-style addressing\.

The following examples show you how to delete an access point using the AWS Management Console and the AWS Command Line Interface \(AWS CLI\)\.

## Using the S3 console<a name="s3-outposts-bucket-delete-ap"></a>

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Outposts access points**\.

1. In the **Outposts access points** section, choose the Outposts access point that you want to delete\.

1. Choose **Delete**\.

1. Confirm the deletion\.

## Using the AWS CLI<a name="S3OutpostsAccessPointsDeleteCLI"></a>

```
aws s3control delete-access-point --account-id 123456789012 --name arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/accesspoint/example-Outposts-Access-Point
```