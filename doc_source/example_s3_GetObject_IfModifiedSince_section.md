# Get an object from an Amazon S3 bucket using an AWS SDK, specifying an If\-Modified\-Since header<a name="example_s3_GetObject_IfModifiedSince_section"></a>

The following code example shows how to read data from an object in an S3 bucket, but only if that bucket has not been modified since the last retrieval time\.

**Note**  
The source code for these examples is in the [AWS Code Examples GitHub repository](https://github.com/awsdocs/aws-doc-sdk-examples)\. Have feedback on a code example? [Create an Issue](https://github.com/awsdocs/aws-doc-sdk-examples/issues/new/choose) in the code examples repo\. 

------
#### [ Rust ]

**SDK for Rust**  
This documentation is for an SDK in preview release\. The SDK is subject to change and should not be used in production\.
 There's more on GitHub\. Find the complete example and learn how to set up and run in the [AWS Code Examples Repository](https://github.com/awsdocs/aws-doc-sdk-examples/tree/main/rust_dev_preview/s3#code-examples)\. 
  

```
use aws_sdk_s3::{
    error::SdkError,
    operation::head_object::HeadObjectError,
    primitives::{ByteStream, DateTime},
    Client, Error,
};
use aws_smithy_types::date_time::Format;
use http::StatusCode;
use tracing::{error, warn};

const KEY: &str = "key";
const BODY: &str = "Hello, world!";

/// Lists your tables in DynamoDB local.
#[tokio::main]
async fn main() -> Result<(), Error> {
    tracing_subscriber::fmt::init();
    let uuid = uuid::Uuid::new_v4();
    let client = Client::new(&aws_config::load_from_env().await);

    // Create bucket.
    // Put an object in the bucket.
    // Get the bucket.
    // Get the bucket again, but it hasn't been modified.
    // Delete the bucket.

    let bucket = format!("if-modified-since-{uuid}");

    client.create_bucket().bucket(bucket.clone()).send().await?;

    let put_object = client
        .put_object()
        .bucket(bucket.as_str())
        .key(KEY)
        .body(ByteStream::from_static(BODY.as_bytes()))
        .send()
        .await;

    // A normal successful 200, which we can grab the etag from.
    let e_tag = match put_object {
        Ok(output) => output.e_tag,
        Err(err) => {
            error!("{err:?}");
            None
        }
    };

    let head_object = client
        .head_object()
        .bucket(bucket.as_str())
        .key(KEY)
        .send()
        .await;

    // Get Object will also 200, and again let us us politely ask for the last_modified and the etag.
    let (last_modified, e_tag_2) = match head_object {
        Ok(output) => (Ok(output.last_modified().cloned()), output.e_tag),
        Err(err) => (Err(err), None),
    };

    warn!("last modified: {last_modified:?}");
    assert_eq!(e_tag, e_tag_2, "Put and first Get had differing etags");

    let head_object = client
        .head_object()
        .bucket(bucket.as_str())
        .key(KEY)
        .if_modified_since(last_modified.unwrap().unwrap())
        .send()
        .await;

    // And then.
    // The second Get Object, with the set `id_modified_since`, is a 304, which is an SdkError::ServiceError.
    // Go through it and pull the parts out (when it's actually a 304).
    let (last_modified, e_tag_2): (
        Result<Option<DateTime>, SdkError<HeadObjectError>>,
        Option<String>,
    ) = match head_object {
        Ok(output) => (Ok(output.last_modified().cloned()), output.e_tag),
        Err(err) => match err {
            SdkError::ServiceError(err) => {
                let http = err.raw().http();
                match http.status() {
                    StatusCode::NOT_MODIFIED => (
                        Ok(Some(
                            DateTime::from_str(
                                http.headers()
                                    .get("last-modified")
                                    .map(|t| t.to_str().unwrap())
                                    .unwrap(),
                                Format::HttpDate,
                            )
                            .unwrap(),
                        )),
                        http.headers()
                            .get("etag")
                            .map(|t| t.to_str().unwrap().into()),
                    ),
                    _ => (Err(SdkError::ServiceError(err)), None),
                }
            }
            _ => (Err(err), None),
        },
    };

    warn!("last modified: {last_modified:?}");
    assert_eq!(e_tag, e_tag_2, "Put and second Get had differing etags");

    client
        .delete_object()
        .bucket(bucket.as_str())
        .key(KEY)
        .send()
        .await?;

    client
        .delete_bucket()
        .bucket(bucket.as_str())
        .send()
        .await?;

    Ok(())
}
```
+  For API details, see [GetObject](https://docs.rs/releases/search?query=aws-sdk) in *AWS SDK for Rust API reference*\. 

------

For a complete list of AWS SDK developer guides and code examples, see [Using this service with an AWS SDK](UsingAWSSDK.md#sdk-general-information-section)\. This topic also includes information about getting started and details about previous SDK versions\.