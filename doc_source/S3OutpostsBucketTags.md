# Adding tags for Amazon S3 on Outposts buckets<a name="S3OutpostsBucketTags"></a>

You can add tags for your S3 on Outposts buckets to track criteria for individual projects or groups of projects\.

**Note**  
The AWS account that creates the bucket owns it and is the only one that can change its tags\.

## Using the S3 console<a name="s3-outposts-add-bucket-tags"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Outposts buckets**\.

1. Choose the Outposts bucket with the tags that you want to edit\.

1. Choose the **Properties** tab\.

1. Under **Tags**, choose **Edit**\.

1. Choose **Add new tag**, and enter the **Key** and optional **Value**\.

   Add any **optional tags** that you would like to associate with an Outposts bucket to track other criteria for individual projects or groups of projects\.

1. Choose **Save changes**\.

## Using the AWS CLI<a name="S3OutpostsBucketTagsCLI"></a>

The following AWS CLI example applies a tagging configuration to an S3 on Outposts bucket using a JSON document in the current folder that specifies tags \(`tagging.json`\)\.

```
aws s3control put-bucket-tagging --account-id 123456789012 --bucket arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/bucket/example-outpost-bucket --tagging file://tagging.json

tagging.json

{
   "TagSet": [
     {
       "Key": "organization",
       "Value": "marketing"
     }
   ]
}
```

The following AWS CLI example applies a tagging configuration to an S3 on Outposts bucket directly from the command line\.

```
aws s3control put-bucket-tagging --account-id 123456789012 --bucket arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/bucket/example-outpost-bucket --tagging 'TagSet=[{Key=organization,Value=marketing}]'
```