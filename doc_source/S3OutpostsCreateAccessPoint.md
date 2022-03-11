# Creating an S3 on Outposts access point<a name="S3OutpostsCreateAccessPoint"></a>

To access your Amazon S3 on Outposts, you must create and configure an access point\.

Access points simplify managing data access at scale for shared datasets in Amazon S3\. Access points are named network endpoints that are attached to buckets that you can use to perform Amazon S3 object operations, such as `GetObject` and `PutObject`\. With S3 on Outposts, you must use access points to access any object in an Outposts bucket\. Access points support only virtual\-host\-style addressing\.

The following examples show you how to create an S3 on Outposts access point using the AWS Management Console, AWS CLI, and AWS SDK for Java\.

**Note**  
The AWS account that creates the Outposts bucket owns it and is the only one that can assign access points to it\.

## Using the S3 console<a name="s3-outposts-bucket-create-accesspoint"></a>

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Outposts buckets**\.

1. Choose the Outposts bucket that you want to create an Outposts access point for\.

1. Choose the **Outposts access points** tab\.

1. In the **Outposts access points** section, choose **Create Outposts access point**\.

1. In **Outposts access point settings**, enter a name for the access point, and choose the virtual private cloud \(VPC\)\.

1. If you want to add a policy for your access point, enter it in the **Outposts access point policy** section\.

   For more information, see [Setting up AWS Identity and Access Management with S3 on Outposts](S3OutpostsIAM.md)\.

## Using the AWS CLI<a name="S3OutpostsCreateAccessPointCLI"></a>

**Example**  
The following AWS CLI example creates an access point for an Outposts bucket\.  

```
aws s3control create-access-point --account-id 123456789012 --name example-Outposts-Access-Point --bucket "arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/bucket/example-outpost-bucket" --vpc-configuration VpcId=example-vpc-12345
```

## Using the AWS SDK for Java<a name="S3OutpostsCreateAccessPointJava"></a>

**Example**  
The following SDK for Java example creates an access point for an Outposts bucket\.  

```
import com.amazonaws.services.s3control.model.*;

public String createAccessPoint(String bucketArn, String accessPointName) {

    CreateAccessPointRequest reqCreateAP = new CreateAccessPointRequest()
            .withAccountId(AccountId)
            .withBucket(bucketArn)
            .withName(accessPointName)
            .withVpcConfiguration(new VpcConfiguration().withVpcId("vpc-12345"));

    CreateAccessPointResult respCreateAP = s3ControlClient.createAccessPoint(reqCreateAP);
    System.out.printf("CreateAccessPoint Response: %s%n", respCreateAP.toString());

    return respCreateAP.getAccessPointArn();

}
```