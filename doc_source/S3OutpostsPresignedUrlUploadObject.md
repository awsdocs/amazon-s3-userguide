# Generating a presigned URL to upload an object to an S3 on Outposts bucket<a name="S3OutpostsPresignedUrlUploadObject"></a>

To grant time\-limited access to objects that are stored locally on an Outpost without updating your bucket policy, you can use a presigned URL\. With presigned URLs, you as the bucket owner can share objects with individuals in your virtual private cloud \(VPC\) or grant them the ability to upload or delete objects\. 

When you create a presigned URL by using the AWS SDKs or the AWS Command Line Interface \(AWS CLI\), you associate the URL with a specific action\. You also grant time\-limited access to the presigned URL by choosing a custom expiration time that can be as low as 1 second and as high as 7 days\. When you share the presigned URL, the individual in the VPC can perform the action embedded in the URL as if they were the original signing user\. When the URL reaches its expiration time, the URL expires and no longer works\.

When you create a presigned URL, you must provide your security credentials, and then specify the following: 
+ An access point Amazon Resource Name \(ARN\) for the Amazon S3 on Outposts bucket
+ An object key
+ An HTTP method \(`PUT` for uploading objects\)
+ An expiration date and time

A presigned URL is valid only for the specified duration\. That is, you must start the action that's allowed by the URL before the expiration date and time\. You can use a presigned URL multiple times, up to the expiration date and time\. If you created a presigned URL by using a temporary token, then the URL expires when the token expires, even if you created the URL with a later expiration time\. 

If the action allowed by a presigned URL consists of multiple steps, such as a multipart upload, you must start all steps before the expiration time\. If S3 on Outposts tries to start a step with an expired URL, you receive an error\.

Users in the virtual private cloud \(VPC\) who have access to the presigned URL can upload objects\. For example, a user in the VPC who has access to the presigned URL can upload an object to your bucket\. Because presigned URLs grant access to your S3 on Outposts bucket to any user in the VPC who has access to the presigned URL, we recommend that you protect these URLs appropriately\. For more details about protecting presigned URLs, see [Limiting presigned URL capabilities](S3OutpostsPresignedURL.md#S3OutpostsPresignedUrlUploadObjectLimitCapabilities)\. 

Anyone with valid security credentials can create a presigned URL\. However, the presigned URL must be created by someone who has permission to perform the operation that the presigned URL is based upon\. For more information, see [Who can create a presigned URL](S3OutpostsPresignedURL.md#S3Outpostswho-presigned-url)\.

## Using the AWS SDKs to generate a presigned URL for an S3 on Outposts object operation<a name="s3-outposts-presigned-urls-upload-examples"></a>

------
#### [ Java ]

**SDK for Java 2\.x**  
This example shows how to generate a presigned URL that you can use to upload an object to an S3 on Outposts bucket for a limited time\. For more information, see [Using presigned URLs for S3 on Outposts](S3OutpostsPresignedURL.md)\.   

```
    public static void signBucket(S3Presigner presigner, String outpostAccessPointArn, String keyName) {

        try {
            PutObjectRequest objectRequest = PutObjectRequest.builder()
                    .bucket(accessPointArn)
                    .key(keyName)
                    .contentType("text/plain")
                    .build();

            PutObjectPresignRequest presignRequest = PutObjectPresignRequest.builder()
                    .signatureDuration(Duration.ofMinutes(10))
                    .putObjectRequest(objectRequest)
                    .build();

            PresignedPutObjectRequest presignedRequest = presigner.presignPutObject(presignRequest);


            String myURL = presignedRequest.url().toString();
            System.out.println("Presigned URL to upload a file to: " +myURL);
            System.out.println("Which HTTP method must be used when uploading a file: " +
                    presignedRequest.httpRequest().method());

            // Upload content to the S3 on Outposts bucket by using this URL.
            URL url = presignedRequest.url();

            // Create the connection and use it to upload the new object by using the presigned URL.
            HttpURLConnection connection = (HttpURLConnection) url.openConnection();
            connection.setDoOutput(true);
            connection.setRequestProperty("Content-Type","text/plain");
            connection.setRequestMethod("PUT");
            OutputStreamWriter out = new OutputStreamWriter(connection.getOutputStream());
            out.write("This text was uploaded as an object by using a presigned URL.");
            out.close();

            connection.getResponseCode();
            System.out.println("HTTP response code is " + connection.getResponseCode());

        } catch (S3Exception e) {
            e.getStackTrace();
        } catch (IOException e) {
            e.getStackTrace();
        }
    }
```

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
This example shows how to generate a presigned URL that can perform an S3 on Outposts action for a limited time\. For more information, see [Using presigned URLs for S3 on Outposts](S3OutpostsPresignedURL.md)\. To make a request with the URL, use the `Requests` package\.  

```
import argparse
import logging
import boto3
from botocore.exceptions import ClientError
import requests

logger = logging.getLogger(__name__)


def generate_presigned_url(s3_client, client_method, method_parameters, expires_in):
    """
    Generate a presigned S3 on Outposts URL that can be used to perform an action.

    :param s3_client: A Boto3 Amazon S3 client.
    :param client_method: The name of the client method that the URL performs.
    :param method_parameters: The parameters of the specified client method.
    :param expires_in: The number of seconds that the presigned URL is valid for.
    :return: The presigned URL.
    """
    try:
        url = s3_client.generate_presigned_url(
            ClientMethod=client_method,
            Params=method_parameters,
            ExpiresIn=expires_in
        )
        logger.info("Got presigned URL: %s", url)
    except ClientError:
        logger.exception(
            "Couldn't get a presigned URL for client method '%s'.", client_method)
        raise
    return url


def usage_demo():
    logging.basicConfig(level=logging.INFO, format='%(levelname)s: %(message)s')

    print('-'*88)
    print("Welcome to the Amazon S3 on Outposts presigned URL demo.")
    print('-'*88)

    parser = argparse.ArgumentParser()
    parser.add_argument('accessPointArn', help="The name of the S3 on Outposts access point ARN.")
    parser.add_argument(
        'key', help="For a GET operation, the key of the object in S3 on Outposts. For a "
                    "PUT operation, the name of a file to upload.")
    parser.add_argument(
        'action', choices=('get', 'put'), help="The action to perform.")
    args = parser.parse_args()

    s3_client = boto3.client('s3')
    client_action = 'get_object' if args.action == 'get' else 'put_object'
    url = generate_presigned_url(
        s3_client, client_action, {'Bucket': args.accessPointArn, 'Key': args.key}, 1000)

    print("Using the Requests package to send a request to the URL.")
    response = None
    if args.action == 'get':
        response = requests.get(url)
    elif args.action == 'put':
        print("Putting data to the URL.")
        try:
            with open(args.key, 'r') as object_file:
                object_text = object_file.read()
            response = requests.put(url, data=object_text)
        except FileNotFoundError:
            print(f"Couldn't find {args.key}. For a PUT operation, the key must be the "
                  f"name of a file that exists on your computer.")

    if response is not None:
        print("Got response:")
        print(f"Status: {response.status_code}")
        print(response.text)

    print('-'*88)


if __name__ == '__main__':
    usage_demo()
```

------