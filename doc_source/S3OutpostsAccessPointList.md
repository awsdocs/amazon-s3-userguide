# View a list of your Amazon S3 on Outposts access points<a name="S3OutpostsAccessPointList"></a>

Access points simplify managing data access at scale for shared datasets in Amazon S3\. Access points are named network endpoints that are attached to buckets that you can use to perform Amazon S3 object operations, such as `GetObject` and `PutObject`\. With S3 on Outposts, you must use access points to access any object in an Outposts bucket\. Access points support only virtual\-host\-style addressing\.

The following topics show you how to return a list of your S3 on Outposts access points by using the AWS Management Console, AWS Command Line Interface \(AWS CLI\), and AWS SDK for Java\.

## Using the S3 console<a name="s3-outposts-bucket-list-accesspoints"></a>

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Outposts access points**\.

1. Under **Outposts access points**, review your list of S3 on Outposts access points\.

## Using the AWS CLI<a name="S3OutpostsListAccessPointCLI"></a>

The following AWS CLI example lists the access points for an Outposts bucket\. To run this command, replace the `user input placeholders` with your own information\.

```
aws s3control list-access-points --account-id 123456789012 --bucket arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/bucket/example-outpost-bucket
```

## Using the AWS SDK for Java<a name="S3OutpostsListAccessPointJava"></a>

The following SDK for Java example lists the access points for an Outposts bucket\.

```
import com.amazonaws.services.s3control.model.*;

public void listAccessPoints(String bucketArn) {

    ListAccessPointsRequest reqListAPs = new ListAccessPointsRequest()
            .withAccountId(AccountId)
            .withBucket(bucketArn);

    ListAccessPointsResult respListAPs = s3ControlClient.listAccessPoints(reqListAPs);
    System.out.printf("ListAccessPoints Response: %s%n", respListAPs.toString());

}
```