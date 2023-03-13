# Generating a presigned URL to upload an object<a name="PresignedUrlUploadObject"></a>

A presigned URL gives you access to the object identified in the URL, provided that the creator of the presigned URL has permissions to access that object\. That is, if you receive a presigned URL to upload an object, you can upload the object only if the creator of the presigned URL has the necessary permissions to upload that object\. 

All objects and buckets by default are private\. The presigned URLs are useful if you want your user/customer to be able to upload a specific object to your bucket, but you don't require them to have AWS security credentials or permissions\. 

When you create a presigned URL, you must provide your security credentials and then specify a bucket name, an object key, an HTTP method \(PUT for uploading objects\), and an expiration date and time\. The presigned URLs are valid only for the specified duration\. That is, you must start the action before the expiration date and time\. 

When uploading a file using PUT, you need to specify the 'Content-Length' HTTP header:

```python
import os
import urllib3
http = urllib3.PoolManager()
fsize = os.path.getsize(PATH)
headers = {'Content-Length': str(fsize)}
with open(PATH, 'rb') as f:
    r = http.request('PUT', S3_PRESIGN_URI, body=f, headers=headers)
```

If the action consists of multiple steps, such as a multipart upload, all steps must be started before the expiration\. Otherwise, you will receive an error when Amazon S3 tries to start a step with an expired URL\.

You can use the presigned URL multiple times, up to the expiration date and time\.

**Access to presigned URLs**  
Because presigned URLs grant access to your Amazon S3 buckets to whoever has the URL, we recommend that you protect them appropriately\. For more information about protecting presigned URLs, see [Limiting presigned URL capabilities](using-presigned-url.md#PresignedUrlUploadObject-LimitCapabilities)\.

Anyone with valid security credentials can create a presigned URL\. However, for you to successfully upload an object, the presigned URL must be created by someone who has permission to perform the operation that the presigned URL is based upon\. For more information, see [Who can create a presigned URL](using-presigned-url.md#who-presigned-url)\.

## Generating a presigned URL for uploading objects<a name="generating-presigned-url"></a>

You can generate a presigned URL programmatically using the AWS SDKs for \.NET, Java, Ruby, [JavaScript](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/S3.html#getSignedUrl-property), PHP, and [Python](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/s3.html#S3.Client.generate_presigned_url)\.

You can use the AWS SDK to generate a presigned URL that you or anyone that you give the URL to can use to upload an object to Amazon S3\. When you use the URL to upload an object, Amazon S3 creates the object in the specified bucket\. If an object with the same key that is specified in the presigned URL already exists in the bucket, Amazon S3 replaces the existing object with the uploaded object\.

### Using AWS Explorer for Visual Studio<a name="upload-object-presignedurl-vsexplorer"></a>

If you're using Microsoft Visual Studio, you can generate a presigned object URL without writing any code by using AWS Explorer in the AWS Toolkit for Visual Studio\. Anyone who receives a valid presigned URL can then programmatically upload an object\. For more information, see [Using Amazon S3 from AWS Explorer](https://docs.aws.amazon.com/toolkit-for-visual-studio/latest/user-guide/tkv-s3.html) in the *AWS Toolkit for Visual Studio User Guide*\. 

For instructions on installing AWS Explorer, see [Developing with Amazon S3 using the AWS SDKs, and explorers](UsingAWSSDK.md)\.

### Using the AWS SDKs<a name="presigned-urls-upload-examples"></a>

The following examples show how to upload objects using presigned URLs\.

The following code examples show how to create a presigned URL for Amazon S3 and upload an object\.

------
#### [ \.NET ]

**AWS SDK for \.NET**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/dotnetv3/S3/#code-examples)\. 
Generate a presigned URL that can perform an Amazon S3 action for a limited time\.  

```
    using System;
    using Amazon;
    using Amazon.S3;
    using Amazon.S3.Model;

    public class GenPresignedUrl
    {
        public static void Main()
        {
            const string bucketName = "doc-example-bucket";
            const string objectKey = "sample.txt";

            // Specify how long the presigned URL lasts, in hours
            const double timeoutDuration = 12;

            // Specify the AWS Region of your Amazon S3 bucket if it is
            // different from the Region defined for the default user,
            // pass the Region to the constructor for the client. For
            // example:
            //      RegionEndpoint bucketRegion = RegionEndpoint.USWest2;
            IAmazonS3 s3Client = new AmazonS3Client();

            string urlString = GeneratePresignedURL(s3Client, bucketName, objectKey, timeoutDuration);
            Console.WriteLine($"The generated URL is: {urlString}.");
        }

        /// <summary>
        /// Generate a presigned URL that can be used to access the file named
        /// in the objectKey parameter for the amount of time specified in the
        /// duration parameter.
        /// </summary>
        /// <param name="client">An initialized S3 client object used to call
        /// the GetPresignedUrl method.</param>
        /// <param name="bucketName">The name of the S3 bucket containing the
        /// object for which to create the presigned URL.</param>
        /// <param name="objectKey">The name of the object to access with the
        /// presigned URL.</param>
        /// <param name="duration">The length of time for which the presigned
        /// URL will be valid.</param>
        /// <returns>A string representing the generated presigned URL.</returns>
        public static string GeneratePresignedURL(IAmazonS3 client, string bucketName, string objectKey, double duration)
        {
            string urlString = string.Empty;
            try
            {
                var request = new GetPreSignedUrlRequest()
                {
                    BucketName = bucketName,
                    Key = objectKey,
                    Expires = DateTime.UtcNow.AddHours(duration),
                };
                urlString = client.GetPreSignedURL(request);
            }
            catch (AmazonS3Exception ex)
            {
                Console.WriteLine($"Error:'{ex.Message}'");
            }

            return urlString;
        }
    }
```
Generate a presigned URL and perform an upload using that URL\.  

```
    using System;
    using System.IO;
    using System.Net;
    using System.Threading.Tasks;
    using Amazon.S3;
    using Amazon.S3.Model;

    public class UploadUsingPresignedURL
    {
        public static void Main()
        {
            string bucketName = "doc-example-bucket";
            string keyName = "samplefile.txt";
            string filePath = $"source\\{keyName}";

            // Specify how long the signed URL will be valid in hours.
            double timeoutDuration = 12;

            // If the AWS Region defined for your default user is different
            // from the Region where your Amazon S3 bucket is located,
            // pass the Region name to the Amazon S3 client object's constructor.
            // For example: RegionEndpoint.USWest2.
            IAmazonS3 client = new AmazonS3Client();

            var url = GeneratePreSignedURL(client, bucketName, keyName, timeoutDuration);
            var success = UploadObject(filePath, url);

            if (success)
            {
                Console.WriteLine("Upload succeeded.");
            }
            else
            {
                Console.WriteLine("Upload failed.");
            }
        }

        /// <summary>
        /// Uploads an object to an Amazon S3 bucket using the presigned URL passed in
        /// the url parameter.
        /// </summary>
        /// <param name="filePath">The path (including file name) to the local
        /// file you want to upload.</param>
        /// <param name="url">The presigned URL that will be used to upload the
        /// file to the Amazon S3 bucket.</param>
        /// <returns>A Boolean value indicating the success or failure of the
        /// operation, based on the HttpWebResponse.</returns>
        public static bool UploadObject(string filePath, string url)
        {
            HttpWebRequest httpRequest = WebRequest.Create(url) as HttpWebRequest;
            httpRequest.Method = "PUT";
            using (Stream dataStream = httpRequest.GetRequestStream())
            {
                var buffer = new byte[8000];
                using (FileStream fileStream = new FileStream(filePath, FileMode.Open, FileAccess.Read))
                {
                    int bytesRead = 0;
                    while ((bytesRead = fileStream.Read(buffer, 0, buffer.Length)) > 0)
                    {
                        dataStream.Write(buffer, 0, bytesRead);
                    }
                }
            }

            HttpWebResponse response = httpRequest.GetResponse() as HttpWebResponse;
            return response.StatusCode == HttpStatusCode.OK;
        }

        /// <summary>
        /// Generates a presigned URL which will be used to upload an object to
        /// an Amazon S3 bucket.
        /// </summary>
        /// <param name="client">The initialized Amazon S3 client object used to call
        /// GetPreSignedURL.</param>
        /// <param name="bucketName">The name of the Amazon S3 bucket to which the
        /// presigned URL will point.</param>
        /// <param name="objectKey">The name of the file that will be uploaded.</param>
        /// <param name="duration">How long (in hours) the presigned URL will
        /// be valid.</param>
        /// <returns>The generated URL.</returns>
        public static string GeneratePreSignedURL(
            IAmazonS3 client,
            string bucketName,
            string objectKey,
            double duration)
        {
            var request = new GetPreSignedUrlRequest
            {
                BucketName = bucketName,
                Key = objectKey,
                Verb = HttpVerb.PUT,
                Expires = DateTime.UtcNow.AddHours(duration),
            };

            string url = client.GetPreSignedURL(request);
            return url;
        }
    }
```

------
#### [ Go ]

**SDK for Go V2**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/gov2/s3#code-examples)\. 
Create functions that wrap S3 presigning actions\.  

```
// Presigner encapsulates the Amazon Simple Storage Service (Amazon S3) presign actions
// used in the examples.
// It contains PresignClient, a client that is used to presign requests to Amazon S3.
// Presigned requests contain temporary credentials and can be made from any HTTP client.
type Presigner struct {
	PresignClient *s3.PresignClient
}



// GetObject makes a presigned request that can be used to get an object from a bucket.
// The presigned request is valid for the specified number of seconds.
func (presigner Presigner) GetObject(
	bucketName string, objectKey string, lifetimeSecs int64) (*v4.PresignedHTTPRequest, error) {
	request, err := presigner.PresignClient.PresignGetObject(context.TODO(), &s3.GetObjectInput{
		Bucket: aws.String(bucketName),
		Key:    aws.String(objectKey),
	}, func(opts *s3.PresignOptions) {
		opts.Expires = time.Duration(lifetimeSecs * int64(time.Second))
	})
	if err != nil {
		log.Printf("Couldn't get a presigned request to get %v:%v. Here's why: %v\n",
			bucketName, objectKey, err)
	}
	return request, err
}



// PutObject makes a presigned request that can be used to put an object in a bucket.
// The presigned request is valid for the specified number of seconds.
func (presigner Presigner) PutObject(
	bucketName string, objectKey string, lifetimeSecs int64) (*v4.PresignedHTTPRequest, error) {
	request, err := presigner.PresignClient.PresignPutObject(context.TODO(), &s3.PutObjectInput{
		Bucket: aws.String(bucketName),
		Key:    aws.String(objectKey),
	}, func(opts *s3.PresignOptions) {
		opts.Expires = time.Duration(lifetimeSecs * int64(time.Second))
	})
	if err != nil {
		log.Printf("Couldn't get a presigned request to put %v:%v. Here's why: %v\n",
			bucketName, objectKey, err)
	}
	return request, err
}



// DeleteObject makes a presigned request that can be used to delete an object from a bucket.
func (presigner Presigner) DeleteObject(bucketName string, objectKey string) (*v4.PresignedHTTPRequest, error) {
	request, err := presigner.PresignClient.PresignDeleteObject(context.TODO(), &s3.DeleteObjectInput{
		Bucket: aws.String(bucketName),
		Key:    aws.String(objectKey),
	})
	if err != nil {
		log.Printf("Couldn't get a presigned request to delete object %v. Here's why: %v\n", objectKey, err)
	}
	return request, err
}
```
Run an interactive example that generates and uses presigned URLs to upload, download, and delete an S3 object\.  

```
// RunPresigningScenario is an interactive example that shows you how to get presigned
// HTTP requests that you can use to move data into and out of Amazon Simple Storage
// Service (Amazon S3). The presigned requests contain temporary credentials and can
// be used by an HTTP client.
//
// 1. Get a presigned request to put an object in a bucket.
// 2. Use the net/http package to use the presigned request to upload a local file to the bucket.
// 3. Get a presigned request to get an object from a bucket.
// 4. Use the net/http package to use the presigned request to download the object to a local file.
// 5. Get a presigned request to delete an object from a bucket.
// 6. Use the net/http package to use the presigned request to delete the object.
//
// This example creates an Amazon S3 presign client from the specified sdkConfig so that
// you can replace it with a mocked or stubbed config for unit testing.
//
// It uses a questioner from the `demotools` package to get input during the example.
// This package can be found in the ..\..\demotools folder of this repo.
//
// It uses an IHttpRequester interface to abstract HTTP requests so they can be mocked
// during testing.
func RunPresigningScenario(sdkConfig aws.Config, questioner demotools.IQuestioner, httpRequester IHttpRequester) {
	defer func() {
		if r := recover(); r != nil {
			fmt.Printf("Something went wrong with the demo.")
		}
	}()

	log.Println(strings.Repeat("-", 88))
	log.Println("Welcome to the Amazon S3 presigning demo.")
	log.Println(strings.Repeat("-", 88))

	s3Client := s3.NewFromConfig(sdkConfig)
	bucketBasics := actions.BucketBasics{S3Client: s3Client}
	presignClient := s3.NewPresignClient(s3Client)
	presigner := actions.Presigner{PresignClient: presignClient}

	bucketName := questioner.Ask("We'll need a bucket. Enter a name for a bucket "+
		"you own or one you want to create:", demotools.NotEmpty{})
	bucketExists, err := bucketBasics.BucketExists(bucketName)
	if err != nil {
		panic(err)
	}
	if !bucketExists {
		err = bucketBasics.CreateBucket(bucketName, sdkConfig.Region)
		if err != nil {
			panic(err)
		} else {
			log.Println("Bucket created.")
		}
	}
	log.Println(strings.Repeat("-", 88))

	log.Printf("Let's presign a request to upload a file to your bucket.")
	uploadFilename := questioner.Ask("Enter the path to a file you want to upload:",
		demotools.NotEmpty{})
	uploadKey := questioner.Ask("What would you like to name the uploaded object?",
		demotools.NotEmpty{})
	uploadFile, err := os.Open(uploadFilename)
	if err != nil {
		panic(err)
	}
	defer uploadFile.Close()
	presignedPutRequest, err := presigner.PutObject(bucketName, uploadKey, 60)
	if err != nil {
		panic(err)
	}
	log.Printf("Got a presigned %v request to URL:\n\t%v\n", presignedPutRequest.Method,
		presignedPutRequest.URL)
	log.Println("Using net/http to send the request...")
	info, err := uploadFile.Stat()
	if err != nil {
		panic(err)
	}
	putResponse, err := httpRequester.Put(presignedPutRequest.URL, info.Size(), uploadFile)
	if err != nil {
		panic(err)
	}
	log.Printf("%v object %v with presigned URL returned %v.", presignedPutRequest.Method,
		uploadKey, putResponse.StatusCode)
	log.Println(strings.Repeat("-", 88))

	log.Printf("Let's presign a request to download the object.")
	questioner.Ask("Press Enter when you're ready.")
	presignedGetRequest, err := presigner.GetObject(bucketName, uploadKey, 60)
	if err != nil {
		panic(err)
	}
	log.Printf("Got a presigned %v request to URL:\n\t%v\n", presignedGetRequest.Method,
		presignedGetRequest.URL)
	log.Println("Using net/http to send the request...")
	getResponse, err := httpRequester.Get(presignedGetRequest.URL)
	if err != nil {
		panic(err)
	}
	log.Printf("%v object %v with presigned URL returned %v.", presignedGetRequest.Method,
		uploadKey, getResponse.StatusCode)
	defer getResponse.Body.Close()
	downloadBody, err := io.ReadAll(getResponse.Body)
	if err != nil {
		panic(err)
	}
	log.Printf("Downloaded %v bytes. Here are the first 100 of them:\n", len(downloadBody))
	log.Println(strings.Repeat("-", 88))
	log.Println(string(downloadBody[:100]))
	log.Println(strings.Repeat("-", 88))

	log.Println("Let's presign a request to delete the object.")
	questioner.Ask("Press Enter when you're ready.")
	presignedDelRequest, err := presigner.DeleteObject(bucketName, uploadKey)
	if err != nil {
		panic(err)
	}
	log.Printf("Got a presigned %v request to URL:\n\t%v\n", presignedDelRequest.Method,
		presignedDelRequest.URL)
	log.Println("Using net/http to send the request...")
	delResponse, err := httpRequester.Delete(presignedDelRequest.URL)
	if err != nil {
		panic(err)
	}
	log.Printf("%v object %v with presigned URL returned %v.\n", presignedDelRequest.Method,
		uploadKey, delResponse.StatusCode)
	log.Println(strings.Repeat("-", 88))

	log.Println("Thanks for watching!")
	log.Println(strings.Repeat("-", 88))
}
```
Define an HTTP request wrapper used by the example to make HTTP requests\.  

```
// IHttpRequester abstracts HTTP requests into an interface so it can be mocked during
// unit testing.
type IHttpRequester interface {
	Get(url string) (resp *http.Response, err error)
	Put(url string, contentLength int64, body io.Reader) (resp *http.Response, err error)
	Delete(url string) (resp *http.Response, err error)
}

// HttpRequester uses the net/http package to make HTTP requests during the scenario.
type HttpRequester struct{}

func (httpReq HttpRequester) Get(url string) (resp *http.Response, err error) {
	return http.Get(url)
}
func (httpReq HttpRequester) Put(url string, contentLength int64, body io.Reader) (resp *http.Response, err error) {
	putRequest, err := http.NewRequest("PUT", url, body)
	if err != nil {
		return nil, err
	}
	putRequest.ContentLength = contentLength
	return http.DefaultClient.Do(putRequest)
}
func (httpReq HttpRequester) Delete(url string) (resp *http.Response, err error) {
	delRequest, err := http.NewRequest("DELETE", url, nil)
	if err != nil {
		return nil, err
	}
	return http.DefaultClient.Do(delRequest)
}
```

------
#### [ Java ]

**SDK for Java 2\.x**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javav2/example_code/s3#readme)\. 
  

```
    public static void signBucket(S3Presigner presigner, String bucketName, String keyName) {

        try {
            PutObjectRequest objectRequest = PutObjectRequest.builder()
                .bucket(bucketName)
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
            System.out.println("Which HTTP method needs to be used when uploading a file: " + presignedRequest.httpRequest().method());

            // Upload content to the Amazon S3 bucket by using this URL.
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

        } catch (S3Exception | IOException e) {
            e.getStackTrace();
        }
    }
```

------
#### [ JavaScript ]

**SDK for JavaScript \(v3\)**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/javascriptv3/example_code/s3#code-examples)\. 
Create a presigned URL to upload an object to a bucket\.  

```
import https from "https";
import { PutObjectCommand, S3Client } from "@aws-sdk/client-s3";
import { fromIni } from "@aws-sdk/credential-providers";
import { HttpRequest } from "@aws-sdk/protocol-http";
import {
  getSignedUrl,
  S3RequestPresigner,
} from "@aws-sdk/s3-request-presigner";
import { parseUrl } from "@aws-sdk/url-parser";
import { formatUrl } from "@aws-sdk/util-format-url";
import { Hash } from "@aws-sdk/hash-node";

const createPresignedUrlWithoutClient = async ({ region, bucket, key }) => {
  const url = parseUrl(`https://${bucket}.s3.${region}.amazonaws.com/${key}`);
  const presigner = new S3RequestPresigner({
    credentials: fromIni(),
    region,
    sha256: Hash.bind(null, "sha256"),
  });

  const signedUrlObject = await presigner.presign(
    new HttpRequest({ ...url, method: "PUT" })
  );
  return formatUrl(signedUrlObject);
};

const createPresignedUrlWithClient = async ({ region, bucket, key }) => {
  const client = new S3Client({ region });
  const command = new PutObjectCommand({ Bucket: bucket, Key: key });
  return getSignedUrl(client, command, { expiresIn: 3600 });
};

function put(url, data) {
  return new Promise((resolve, reject) => {
    const req = https.request(
      url,
      { method: "PUT", headers: { "Content-Length": new Blob([data]).size } },
      (res) => {
        let responseBody = "";
        res.on("data", (chunk) => {
          responseBody += chunk;
        });
        res.on("end", () => {
          resolve(responseBody);
        });
      }
    );
    req.on("error", (err) => {
      reject(err);
    });
    req.write(data);
    req.end();
  });
}

export const main = async () => {
  const REGION = "us-east-1";
  const BUCKET = "coreys-default-bucket";
  const KEY = "corey_test.txt";

  // There are two ways to generate a presigned URL.
  // 1. Use createPresignedUrl without the S3 client.
  // 2. Use getSignedUrl in conjunction with the S3 client and GetObjectCommand.
  try {
    const noClientUrl = await createPresignedUrlWithoutClient({
      region: REGION,
      bucket: BUCKET,
      key: KEY,
    });

    const clientUrl = await createPresignedUrlWithClient({
      region: REGION,
      bucket: BUCKET,
      key: KEY,
    });

    // After you get the presigned URL, you can provide your own file
    // data. Refer to put() above.
    console.log("Calling PUT using presigned URL without client");
    await put(noClientUrl, "Hello World");

    console.log("Calling PUT using presigned URL with client");
    await put(clientUrl, "Hello World");

    console.log("\nDone. Check your S3 console.");
  } catch (err) {
    console.error(err);
  }
};
```
Create a presigned URL to download an object from a bucket\.  

```
import { GetObjectCommand, S3Client } from "@aws-sdk/client-s3";
import { fromIni } from "@aws-sdk/credential-providers";
import { HttpRequest } from "@aws-sdk/protocol-http";
import {
  getSignedUrl,
  S3RequestPresigner,
} from "@aws-sdk/s3-request-presigner";
import { parseUrl } from "@aws-sdk/url-parser";
import { formatUrl } from "@aws-sdk/util-format-url";
import { Hash } from "@aws-sdk/hash-node";

const createPresignedUrlWithoutClient = async ({ region, bucket, key }) => {
  const url = parseUrl(`https://${bucket}.s3.${region}.amazonaws.com/${key}`);
  const presigner = new S3RequestPresigner({
    credentials: fromIni(),
    region,
    sha256: Hash.bind(null, "sha256"),
  });

  const signedUrlObject = await presigner.presign(new HttpRequest(url));
  return formatUrl(signedUrlObject);
};

const createPresignedUrlWithClient = async ({ region, bucket, key }) => {
  const client = new S3Client({ region });
  const command = new GetObjectCommand({ Bucket: bucket, Key: key });
  return getSignedUrl(client, command, { expiresIn: 3600 });
};

export const main = async () => {
  const REGION = "us-east-1";
  const BUCKET = "coreys-default-bucket";
  const KEY = "corey_mug.jpg";

  try {
    const noClientUrl = await createPresignedUrlWithoutClient({
      region: REGION,
      bucket: BUCKET,
      key: KEY,
    });

    const clientUrl = await createPresignedUrlWithClient({
      region: REGION,
      bucket: BUCKET,
      key: KEY,
    });

    console.log("Presigned URL without client");
    console.log(noClientUrl);
    console.log("\n");

    console.log("Presigned URL with client");
    console.log(clientUrl);
  } catch (err) {
    console.error(err);
  }
};
```
+  For more information, see [AWS SDK for JavaScript Developer Guide](https://docs.aws.amazon.com/sdk-for-javascript/v3/developer-guide/s3-example-creating-buckets.html#s3-create-presigendurl)\. 

------
#### [ Python ]

**SDK for Python \(Boto3\)**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/python/example_code/s3/s3_basics#code-examples)\. 
Generate a presigned URL that can perform an S3 action for a limited time\. Use the Requests package to make a request with the URL\.  

```
import argparse
import logging
import boto3
from botocore.exceptions import ClientError
import requests

logger = logging.getLogger(__name__)


def generate_presigned_url(s3_client, client_method, method_parameters, expires_in):
    """
    Generate a presigned Amazon S3 URL that can be used to perform an action.

    :param s3_client: A Boto3 Amazon S3 client.
    :param client_method: The name of the client method that the URL performs.
    :param method_parameters: The parameters of the specified client method.
    :param expires_in: The number of seconds the presigned URL is valid for.
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
    print("Welcome to the Amazon S3 presigned URL demo.")
    print('-'*88)

    parser = argparse.ArgumentParser()
    parser.add_argument('bucket', help="The name of the bucket.")
    parser.add_argument(
        'key', help="For a GET operation, the key of the object in Amazon S3. For a "
                    "PUT operation, the name of a file to upload.")
    parser.add_argument(
        'action', choices=('get', 'put'), help="The action to perform.")
    args = parser.parse_args()

    s3_client = boto3.client('s3')
    client_action = 'get_object' if args.action == 'get' else 'put_object'
    url = generate_presigned_url(
        s3_client, client_action, {'Bucket': args.bucket, 'Key': args.key}, 1000)

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
Generate a presigned POST request to upload a file\.  

```
class BucketWrapper:
    """Encapsulates S3 bucket actions."""
    def __init__(self, bucket):
        """
        :param bucket: A Boto3 Bucket resource. This is a high-level resource in Boto3
                       that wraps bucket actions in a class-like structure.
        """
        self.bucket = bucket
        self.name = bucket.name

    def generate_presigned_post(self, object_key, expires_in):
        """
        Generate a presigned Amazon S3 POST request to upload a file.
        A presigned POST can be used for a limited time to let someone without an AWS
        account upload a file to a bucket.

        :param object_key: The object key to identify the uploaded object.
        :param expires_in: The number of seconds the presigned POST is valid.
        :return: A dictionary that contains the URL and form fields that contain
                 required access data.
        """
        try:
            response = self.bucket.meta.client.generate_presigned_post(
                Bucket=self.bucket.name, Key=object_key, ExpiresIn=expires_in)
            logger.info("Got presigned POST URL: %s", response['url'])
        except ClientError:
            logger.exception(
                "Couldn't get a presigned POST URL for bucket '%s' and object '%s'",
                self.bucket.name, object_key)
            raise
        return response
```

------
#### [ Ruby ]

**SDK for Ruby**  
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/ruby/example_code/s3#code-examples)\. 
  

```
require "aws-sdk-s3"
require "net/http"

# Creates a presigned URL that can be used to upload content to an object.
#
# @param bucket [Aws::S3::Bucket] An existing Amazon S3 bucket.
# @param object_key [String] The key to give the uploaded object.
# @return [URI, nil] The parsed URI if successful; otherwise nil.
def get_presigned_url(bucket, object_key)
  url = bucket.object(object_key).presigned_url(:put)
  puts "Created presigned URL: #{url}"
  URI(url)
rescue Aws::Errors::ServiceError => e
  puts "Couldn't create presigned URL for #{bucket.name}:#{object_key}. Here's why: #{e.message}"
end

def run_demo
  bucket_name = "doc-example-bucket"
  object_key = "my-file.txt"
  object_content = "This is the content of my-file.txt."

  bucket = Aws::S3::Bucket.new(bucket_name)
  presigned_url = get_presigned_url(bucket, object_key)
  return unless presigned_url

  response = Net::HTTP.start(presigned_url.host) do |http|
    http.send_request("PUT", presigned_url.request_uri, object_content, "content_type" => "")
  end

  case response
  when Net::HTTPSuccess
    puts "Content uploaded!"
  else
    puts response.value
  end
end

run_demo if $PROGRAM_NAME == __FILE__
```

------
