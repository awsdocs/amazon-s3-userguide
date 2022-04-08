# Adding or editing an access point policy<a name="S3OutpostsAccessPointEditPolicy"></a>

Access points have distinct permissions and network controls that Amazon S3 on Outposts applies for any request that is made through that access point\. Each access point enforces a customized access point policy that works in conjunction with the bucket policy that is attached to the underlying bucket\. For more information, see [Access points](S3OutpostsWorkingBuckets.md#S3OutpostsAP)\.

The following topics show you how to add or edit the access point policy for your S3 on Outposts access point by using the AWS Management Console, AWS Command Line Interface \(AWS CLI\), and AWS SDK for Java\.

## Using the S3 console<a name="s3-outposts-bucket-edit-accesspoint"></a>

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Outposts buckets**\.

1. Choose the Outposts bucket that you want to edit the access point policy for\.

1. Choose the **Outposts access points** tab\.

1. In the **Outposts access points** section, choose the access point whose policy you want to edit, and choose **Edit policy**\.

1. Add or edit the policy in the **Outposts access point policy** section\. For more information, see [Setting up AWS Identity and Access Management with S3 on Outposts](S3OutpostsIAM.md)\.

## Using the AWS CLI<a name="S3OutpostsPutAccessPointPolicyCLI"></a>

The following AWS CLI example puts a policy on an Outposts access point\.

1. Save the following access point policy to a JSON file\. In this example, the file is named `appolicy1.json`\. Replace the `user input placeholders` with your own information\.

   ```
   {
      "Version":"2012-10-17",
      "Id":"exampleAccessPointPolicy",
      "Statement":[
         {
            "Sid":"st1",
            "Effect":"Allow",
            "Principal":{
               "AWS":"123456789012"
            },
            "Action":"s3-outposts:*",
            "Resource":"arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/accesspoint/example-outposts-access-point
         }
      ]
   }
   ```

1. Submit the JSON file as part of the `put-access-point-policy` CLI command\. Replace the `user input placeholders` with your own information\.

   ```
   aws s3control put-access-point-policy --account-id 123456789012 --name arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/accesspoint/example-outposts-access-point --policy file://appolicy1.json
   ```

## Using the AWS SDK for Java<a name="S3OutpostsPutAccessPointPolicyJava"></a>

The following SDK for Java example puts a policy on an Outposts access point\.

```
import com.amazonaws.services.s3control.model.*;

public void putAccessPointPolicy(String accessPointArn) {

    String policy = "{\"Version\":\"2012-10-17\",\"Id\":\"testAccessPointPolicy\",\"Statement\":[{\"Sid\":\"st1\",\"Effect\":\"Allow\",\"Principal\":{\"AWS\":\"" + AccountId + "\"},\"Action\":\"s3-outposts:*\",\"Resource\":\"" + accessPointArn + "\"}]}";

    PutAccessPointPolicyRequest reqPutAccessPointPolicy = new PutAccessPointPolicyRequest()
            .withAccountId(AccountId)
            .withName(accessPointArn)
            .withPolicy(policy);

    PutAccessPointPolicyResult respPutAccessPointPolicy = s3ControlClient.putAccessPointPolicy(reqPutAccessPointPolicy);
    System.out.printf("PutAccessPointPolicy Response: %s%n", respPutAccessPointPolicy.toString());
    printWriter.printf("PutAccessPointPolicy Response: %s%n", respPutAccessPointPolicy.toString());

}
```