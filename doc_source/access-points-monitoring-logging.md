# Monitoring and logging access points<a name="access-points-monitoring-logging"></a>

Amazon S3 logs requests made through access points and requests made to the APIs that manage access points, such as `CreateAccessPoint` and `GetAccessPointPolicy`\.
+ A bucket named `DOC-EXAMPLE-BUCKET1` in Region `us-west-2` that contains object `my-image.jpg`
+ An access point named `my-bucket-ap` that is associated with `my-bucket`
+ Your AWS account ID is `123456789012`

CloudTrail log entries for requests made through access points will include the access point ARN in the `resources` section of the log\.

```
"resources": [
        {
            "type": "AWS::S3::Object",
            "ARN": "arn:aws:s3:::DOC-EXAMPLE-BUCKET1/my-image.jpg"
        },
        {
            "accountId": "123456789012",
            "type": "AWS::S3::Bucket",
            "ARN": "arn:aws:s3:::DOC-EXAMPLE-BUCKET1"
        },
        {
            "accountId": "DOC-EXAMPLE-BUCKET1",
            "type": "AWS::S3::AccessPoint",
            "ARN": "arn:aws:s3:us-west-2:DOC-EXAMPLE-BUCKET1:accesspoint/my-bucket-ap"
        }
    ]
```

For more information about S3 Server Access Logs, see [Logging requests using server access logging](ServerLogs.md)\. For more information about AWS CloudTrail, see [What is AWS CloudTrail?](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html) in the *AWS CloudTrail User Guide*\.

**Note**  
S3 access points aren't currently compatible with Amazon CloudWatch metrics\.