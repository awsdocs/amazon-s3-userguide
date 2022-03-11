# Deleting objects in Amazon S3 on Outposts buckets<a name="S3OutpostsDeleteObject"></a>

Objects are the fundamental entities stored in S3 on Outposts\. Every object is contained in a bucket\. You must use access points to access any object in an Outpost bucket\. When you specify the bucket for object operations, you use the access point Amazon Resource Name \(ARN\), which includes the AWS Region code for the Region the Outpost is homed to, AWS account ID, Outpost ID, and access point name\. The following example shows the ARN format for S3 on Outposts access points in object operations:

```
arn:aws:s3-outposts:region:account-id:outpost/outpost-id/accesspoint/accesspoint-name
```

With S3 on Outposts, object data is always stored on the Outpost\. When AWS installs an Outpost rack, your data stays local to your Outpost to meet data residency requirements\. Your objects never leave your Outpost and are not in an AWS Region\. Because the AWS Management Console is hosted in\-Region, you can't use the console to upload or manage objects in your Outpost\. However, you can use the REST API, AWS CLI, and AWS SDKs to upload and manage your objects through your access points\.

The following examples show you how to a single object or multiple objects in an S3 on Outposts bucket using the AWS Command Line Interface \(AWS CLI\) and AWS SDK for Java\.

## Using the AWS CLI<a name="S3OutpostsDeleteObjectsCLI"></a>

The following examples show you how to delete a single object or multiple objects from an S3 on Outposts bucket\.

When using these action with Amazon S3 on Outposts through the AWS SDKs, you provide the Outposts access point ARN in place of the bucket name, in the following form: `arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/accesspoint/example-Outposts-Access-Point`\. For more information about S3 on Outposts ARNs, see [ARNs for S3 on Outposts](S3OutpostsIAM.md#S3OutpostsARN)\.

------
#### [ delete\-object ]

The following example deletes an object named `sample-object.xml` from an S3 on Outposts bucket \(`s3-outposts:DeleteObject`\) using the AWS CLI\.

```
aws s3api delete-object --bucket arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/accesspoint/example-Outposts-Access-Point --key sample-object.xml
```

------
#### [ delete\-objects ]

The following example deletes two objects named `sample-object.xml` and `test1.text` from an S3 on Outposts bucket \(`s3-outposts:DeleteObject`\) using the AWS CLI\. For more information about this action, see [delete\-objects](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3api/delete-objects.html) in the *AWS CLI Reference*\.

```
aws s3api delete-objects --bucket arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/accesspoint/example-Outposts-Access-Point --delete file://delete.json

delete.json
{
  "Objects": [
    {
      "Key": "test1.txt"
    },
    {
      "Key": "sample-object.xml"
    }
  ],
  "Quiet": false
}
```

------

## Using the AWS SDK for Java<a name="S3OutpostsDeleteObjectsJava"></a>

The following examples show you how to delete a single object or multiple objects from an S3 on Outposts bucket\.

------
#### [ DeleteObject ]

The following S3 on Outposts example deletes an object in a bucket using the SDK for Java\. 

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.DeleteObjectRequest;

public class DeleteObject {
    public static void main(String[] args) {
        String accessPointArn = "*** access point ARN ***";
        String keyName = "*** Key name ****";

        try {
            // This code expects that you have AWS credentials set up per:
            // https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .enableUseArnRegion()
                    .build();

            s3Client.deleteObject(new DeleteObjectRequest(accessPointArn, keyName));
        } catch (AmazonServiceException e) {
            // The call was transmitted successfully, but Amazon S3 couldn't process
            // it, so it returned an error response.
            e.printStackTrace();
        } catch (SdkClientException e) {
            // Amazon S3 couldn't be contacted for a response, or the client
            // couldn't parse the response from Amazon S3.
            e.printStackTrace();
        }
    }
}
```

------
#### [ DeleteObjects ]

The following S3 on Outposts example uploads and then deletes objects in a bucket using the SDK for Java\. 

```
import com.amazonaws.AmazonServiceException;
import com.amazonaws.SdkClientException;
import com.amazonaws.services.s3.AmazonS3;
import com.amazonaws.services.s3.AmazonS3ClientBuilder;
import com.amazonaws.services.s3.model.DeleteObjectsRequest;
import com.amazonaws.services.s3.model.DeleteObjectsRequest.KeyVersion;
import com.amazonaws.services.s3.model.DeleteObjectsResult;

import java.util.ArrayList;

public class DeleteObjects {

    public static void main(String[] args) {
//        String accessPointArn = "*** access point ARN ***";
        String accessPointArn = "arn:aws:s3-outposts:us-east-1:785856369849:outpost/ec2/accesspoint/mig-test-60";

        try {
            // This code expects that you have AWS credentials set up per:
            // https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/setup-credentials.html
            AmazonS3 s3Client = AmazonS3ClientBuilder.standard()
                    .enableUseArnRegion()
                    .build();

            // Upload three sample objects.
            ArrayList<KeyVersion> keys = new ArrayList<KeyVersion>();
            for (int i = 0; i < 3; i++) {
                String keyName = "delete object example " + i;
                s3Client.putObject(accessPointArn, keyName, "Object number " + i + " to be deleted.");
                keys.add(new KeyVersion(keyName));
            }
            System.out.println(keys.size() + " objects successfully created.");

            // Delete the sample objects.
            DeleteObjectsRequest multiObjectDeleteRequest = new DeleteObjectsRequest(accessPointArn)
                    .withKeys(keys)
                    .withQuiet(false);

            // Verify that the objects were deleted successfully.
            DeleteObjectsResult delObjRes = s3Client.deleteObjects(multiObjectDeleteRequest);
            int successfulDeletes = delObjRes.getDeletedObjects().size();
            System.out.println(successfulDeletes + " objects successfully deleted.");
        } catch (AmazonServiceException e) {
            // The call was transmitted successfully, but Amazon S3 couldn't process
            // it, so it returned an error response.
            e.printStackTrace();
        } catch (SdkClientException e) {
            // Amazon S3 couldn't be contacted for a response, or the client
            // couldn't parse the response from Amazon S3.
            e.printStackTrace();
        }
    }
}
```

------