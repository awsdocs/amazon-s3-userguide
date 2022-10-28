# Adding tags for S3 on Outposts buckets<a name="S3OutpostsBucketTags"></a>

You can add tags for your Amazon S3 on Outposts buckets to track storage costs and other criteria for individual projects or groups of projects\.

**Note**  
The AWS account that creates the bucket owns it and is the only one that can change its tags\.

## Using the S3 console<a name="s3-outposts-add-bucket-tags"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Outposts buckets**\.

1. Choose the Outposts bucket whose tags you want to edit\.

1. Choose the **Properties** tab\.

1. Under **Tags**, choose **Edit**\.

1. Choose **Add new tag**, and enter the **Key** and optional **Value**\.

   Add any tags that you would like to associate with an Outposts bucket to track other criteria for individual projects or groups of projects\.

1. Choose **Save changes**\.

## Using the AWS CLI<a name="S3OutpostsBucketTagsCLI"></a>

The following AWS CLI example applies a tagging configuration to an S3 on Outposts bucket by using a JSON document in the current folder that specifies tags \(`tagging.json`\)\. To use this example, replace each `user input placeholder` with your own information\.

```
aws s3control put-bucket-tagging --account-id 123456789012 --bucket arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/bucket/example-outposts-bucket --tagging file://tagging.json

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
aws s3control put-bucket-tagging --account-id 123456789012 --bucket arn:aws:s3-outposts:region:123456789012:outpost/op-01ac5d28a6a232904/bucket/example-outposts-bucket --tagging 'TagSet=[{Key=organization,Value=marketing}]'
```

For more information about this command, see [put\-bucket\-tagging](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/s3control/put-bucket-tagging.html) in the *AWS CLI Reference*\.