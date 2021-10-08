# Using S3 Intelligent\-Tiering<a name="using-intelligent-tiering"></a>

You can use the S3 Intelligent\-Tiering storage class to automatically optimize storage costs\. S3 Intelligent\-Tiering delivers automatic cost savings by moving data on a granular object level between access tiers when access patterns change\. For data that can be accessed asynchronously, you can choose to enable automatic archiving within the S3 Intelligent\-Tiering storage class using the AWS Management Console, AWS CLI, or Amazon S3 API\.

## Moving data to S3 Intelligent\-Tiering<a name="moving-data-to-int-tiering"></a>

There are two ways to move data into S3 Intelligent\-Tiering\. You can directly [PUT](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html) data into S3 Intelligent\-Tiering by specifying `INTELLIGENT_TIERING` in the `x-amz-storage-class` header or configure S3 Lifecycle policies to transition objects from S3 Standard or S3 Standard\-Infrequent Access to S3 Intelligent\-Tiering\.

### Uploading data to S3 Intelligent\-Tiering using DirectPUT<a name="moving-data-to-int-tiering-directPUT"></a>

When you upload an object to the S3 Intelligent\-Tiering storage class using the [PUT](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html) API operation, you specify S3 Intelligent\-Tiering in the [https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html#API_PutObject_RequestSyntax](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html#API_PutObject_RequestSyntax) request header\.

The following request stores the image, `my-image.jpg`, in the `myBucket` bucket\. The request uses the `x-amz-storage-class` header to request that the object is stored using the S3 Intelligent\-Tiering storage class\. 

**Example**  

```
PUT /my-image.jpg HTTP/1.1
Host: myBucket.s3.<Region>.amazonaws.com (http://amazonaws.com/)
Date: Wed, 1 Sep 2021 17:50:00 GMT
Authorization: authorization string
Content-Type: image/jpeg
Content-Length: 11434
Expect: 100-continue
x-amz-storage-class: INTELLIGENT_TIERING
```

### Transitioning data to S3 Intelligent\-Tiering from S3 Standard or S3 Standard\-Infrequent Access using S3 Lifecycle<a name="moving-data-to-int-tiering-lifecycle"></a>

You can add rules to an S3 Lifecycle configuration to tell Amazon S3 to transition objects from one storage class to another\. For information on supported transitions and related constraints, see [ Transitioning objects using S3 Lifecycle](https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-transition-general-considerations.html)\. 

You can specify S3 Lifecycle policies at the bucket or prefix level\. In this S3 Lifecycle configuration rule, the filter specifies a key prefix \(`documents/`\)\. Therefore, the rule applies to objects with key name prefix `documents/`, such as `documents/doc1.txt` and `documents/doc2.txt`\. The rule specifies a `Transition` action directing Amazon S3 to transition objects to the S3 Intelligent\-Tiering storage class 0 days after creation\. In this case, objects are eligible for transition to S3 Intelligent\-Tiering at midnight UTC following creation\.

**Example**  

```
<LifecycleConfiguration>
  <Rule>
    <ID>ExampleRule</ID>
    <Filter>
       <Prefix>documents/</Prefix>
    </Filter>
    <Status>Enabled</Status>
    <Transition>
      <Days>0</Days>
      <StorageClass>INTELLIGENT_TIERING</StorageClass>
    </Transition>
 </Rule>
</LifecycleConfiguration>
```

## Enabling S3 Intelligent\-Tiering automatic archiving<a name="enable-auto-archiving-int-tiering"></a>

You can activate one or both of the archive access tiers by creating a bucket, prefix, or object tag level configuration using the AWS Management Console, AWS CLI, or Amazon S3 API\. 

### Using the S3 console<a name="enable-auto-archiving-int-tiering-console"></a>

**To enable S3 Intelligent\-Tiering automatic archiving**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the **Buckets** list, choose the name of the bucket that you want\.

1. Choose **Properties**\.

1. Navigate to the **S3 Intelligent\-Tiering Archive configurations** section and choose **Create configuration**\.

1. In the **Archive configuration settings** section, specify a descriptive configuration name for your S3 Intelligent\-Tiering Archive configuration\.

1. Under **Choose a configuration scope**, choose a configuration scope to use\. Optionally, you can limit the configuration scope to specified objects within a bucket using a shared prefix, object tag, or combination of the two\.

   1. To limit the scope of the configuration, select **Limit the scope of this configuration using one or more filters**\.

   1. To limit the scope of the configuration using a single prefix, enter the prefix under **Prefix**\. 

   1. To limit the scope of the configuration using object tags, select **Add tag** and enter a value for Key\.

1. Under **Status**, select **Enable**\.

1. In the **Archive settings** section, select one or both of the Archive Access tiers to enable\.

1. Choose **Create**\.

### Using the AWS CLI<a name="enable-auto-archiving-int-tiering-cli"></a>

You can use the following AWS CLI commands to manage S3 Intelligent\-Tiering configurations:
+ `put-bucket-intelligent-tiering`
+ `get-bucket-intelligent-tiering`
+ `delete-intelligent-tiering`
+ `list-intelligent-tiering`

For instructions on setting up the AWS CLI, see [Developing with Amazon S3 using the AWS CLI](setup-aws-cli.md)\.

When using the AWS CLI, you cannot specify the configuration as an XML file\. You must specify the JSON instead\. The following is an example XML S3 Intelligent\-Tiering configuration and equivalent JSON that you can specify in an AWS CLI command\.

The following example puts an S3 Intelligent\-Tiering configuration to the specified bucket\.

**Example [https://docs.aws.amazon.com/cli/latest/reference/s3api/put-bucket-intelligent-tiering-configuration.html](https://docs.aws.amazon.com/cli/latest/reference/s3api/put-bucket-intelligent-tiering-configuration.html) configuration**  

```
{
  "Id": "string",
  "Filter": {
    "Prefix": "string",
    "Tag": {
      "Key": "string",
      "Value": "string"
    },
    "And": {
      "Prefix": "string",
      "Tags": [
        {
          "Key": "string",
          "Value": "string"
        }
        ...
      ]
    }
  },
  "Status": "Enabled"|"Disabled",
  "Tierings": [
    {
      "Days": integer,
      "AccessTier": "ARCHIVE_ACCESS"|"DEEP_ARCHIVE_ACCESS"
    }
    ...
  ]
}
```

```
PUT /?intelligent-tiering&id=Id HTTP/1.1
Host: Bucket.s3.amazonaws.com
<?xml version="1.0" encoding="UTF-8"?>
<IntelligentTieringConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
   <Id>string</Id>
   <Filter>
      <And>
         <Prefix>string</Prefix>
         <Tag>
            <Key>string</Key>
            <Value>string</Value>
         </Tag>
         ...
      </And>
      <Prefix>string</Prefix>
      <Tag>
         <Key>string</Key>
         <Value>string</Value>
      </Tag>
   </Filter>
   <Status>string</Status>
   <Tiering>
      <AccessTier>string</AccessTier>
      <Days>integer</Days>
   </Tiering>
   ...
</IntelligentTieringConfiguration>
```

### Using the PUT API operation<a name="enable-auto-archiving-int-tiering-api"></a>

You can use the [ `PutBucketIntelligentTieringConfiguration`](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutBucketIntelligentTieringConfiguration.html) operation for a specified bucket and up to 1,000 S3 Intelligent\-Tiering configurations per bucket\. You can define which objects within a bucket are eligible for the archive access tiers using a shared prefix or object tag\. Using a shared prefix or object tag allows you to align to specific business applications, workflows, or internal organizations\. You also have the flexibility to activate the Archive Access tier, the Deep Archive Access tier, or both\.