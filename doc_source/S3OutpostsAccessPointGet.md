# Viewing information about an access point configuration<a name="S3OutpostsAccessPointGet"></a>

Access points simplify managing data access at scale for shared datasets in Amazon S3\. Access points are named network endpoints that are attached to buckets that you can use to perform Amazon S3 object operations, such as `GetObject` and `PutObject`\. With S3 on Outposts, you must use access points to access any object in an Outposts bucket\. Access points support only virtual\-host\-style addressing\.

The following topics show you how to return configuration information for an S3 on Outposts access point using the AWS Management Console, AWS Command Line Interface \(AWS CLI\), and AWS SDK for Java\.

## Using the S3 console<a name="s3-outposts-bucket-get-accesspoint"></a>

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Outposts access points**\.

1. Choose the Outposts access point that you want to view configuration details for\.

1. Under **Outposts access point overview**, review the access point configuration details\.

## Using the AWS CLI<a name="S3OutpostsGetAccessPointCLI"></a>

The following AWS CLI example gets an access point for an Outposts bucket\.

```
aws s3control get-access-point --account-id 123456789012 --name arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/accesspoint/example-Outposts-Access-Point
```

## Using the AWS SDK for Java<a name="S3OutpostsGetAccessPointJava"></a>

The following SDK for Java example gets an access point for an Outposts bucket\.

```
import com.amazonaws.services.s3control.model.*;

public void getAccessPoint(String accessPointArn) {

    GetAccessPointRequest reqGetAP = new GetAccessPointRequest()
            .withAccountId(AccountId)
            .withName(accessPointArn);

    GetAccessPointResult respGetAP = s3ControlClient.getAccessPoint(reqGetAP);
    System.out.printf("GetAccessPoint Response: %s%n", respGetAP.toString());

}
```