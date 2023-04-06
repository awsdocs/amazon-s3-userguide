# Protecting data by using client\-side encryption<a name="UsingClientSideEncryption"></a>

*Client\-side encryption* is the act of encrypting your data locally to help ensure its security in transit and at rest\. To encrypt your objects before you send them to Amazon S3, use the Amazon S3 Encryption Client\. When your objects are encrypted in this manner, your objects aren't exposed to any third party, including AWS\. Amazon S3 receives your objects already encrypted; Amazon S3 does not play a role in encrypting or decrypting your objects\. You can use both the Amazon S3 Encryption Client and [server\-side encryption](serv-side-encryption.md) to encrypt your data\. When you send encrypted objects to Amazon S3, Amazon S3 doesn't recognize the objects as being encrypted, it only detects typical objects\.

The Amazon S3 Encryption Client works as an intermediary between you and Amazon S3\. After you instantiate the Amazon S3 Encryption Client, your objects are automatically encrypted and decrypted as part of your Amazon S3 `PutObject` and `GetObject` requests\. Your objects are all encrypted with a unique data key\. The Amazon S3 Encryption Client does not use or interact with bucket keys, even if you specify a KMS key as your wrapping key\.

The *Amazon S3 Encryption Client Developer Guide* focuses on version 3\.x of the Amazon S3 Encryption Client for Java, which is an independent library provided in the [amazon\-s3\-encryption\-client\-java](https://github.com/aws/amazon-s3-encryption-client-java/) GitHub repository\. For more information, see [What is the Amazon S3 Encryption Client?](https://docs.aws.amazon.com/amazon-s3-encryption-client/latest/developerguide/what-is-s3-encryption-client.html) in the *Amazon S3 Encryption Client Developer Guide*\.

For more information about other language implementations, see the AWS SDK Developer Guide for your programming language\.
+ [AWS SDK for \.NET](https://docs.aws.amazon.com/sdk-for-net/v3/developer-guide/kms-keys-s3-encryption.html)
+ [AWS SDK for Go](https://docs.aws.amazon.com/sdk-for-go/v1/developer-guide/welcome.html)
+ [AWS SDK for PHP](https://docs.aws.amazon.com/sdk-for-php/v3/developer-guide/s3-encryption-client.html)
+ [AWS SDK for Ruby](https://docs.aws.amazon.com/sdk-for-ruby/v3/api/Aws/S3/Encryption.html)
+ [AWS SDK for C\+\+](https://docs.aws.amazon.com/sdk-for-cpp/v1/developer-guide/welcome.html)