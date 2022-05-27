# Configure the S3 control client for S3 on Outposts by using the SDK for Java<a name="S3OutpostsCongfigureS3ControlClientJava"></a>

The following example configures the Amazon S3 control client for Amazon S3 on Outposts by using the AWS SDK for Java\. To use this example, replace each `user input placeholder` with your own information\. 

```
import com.amazonaws.auth.AWSStaticCredentialsProvider;
import com.amazonaws.auth.BasicAWSCredentials;
import com.amazonaws.services.s3control.AWSS3Control;
import com.amazonaws.services.s3control.AWSS3ControlClient;

public AWSS3Control createS3ControlClient() {

    String accessKey = AWSAccessKey;
    String secretKey = SecretAccessKey;
    BasicAWSCredentials awsCreds = new BasicAWSCredentials(accessKey, secretKey);

    return AWSS3ControlClient.builder().enableUseArnRegion()
            .withCredentials(new AWSStaticCredentialsProvider(awsCreds))
            .build();

}
```