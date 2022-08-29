# Actions, resources, and condition keys for Amazon S3<a name="list_amazons3"></a>

Amazon S3 \(service prefix: `s3`\) provides the following service\-specific resources, actions, and condition context keys for use in IAM permission policies\.

**Note**  
You can use the actions listed below in IAM policies and Amazon S3 bucket policies to grant permissions for specific Amazon S3 API operations\. Most actions have the same name as the API operations they are associated with\. However, in some cases, the API operation and action names are different\. Additionally, a single action can control access to more than one operation, and some operations require several different actions\. 

References:
+ Learn how to [configure this service](https://docs.aws.amazon.com/AmazonS3/latest/dev/)\.
+ View a list of the [API operations available for this service](https://docs.aws.amazon.com/AmazonS3/latest/API/)\.
+ Learn how to secure this service and its resources by [using IAM](https://docs.aws.amazon.com/AmazonS3/latest/dev/access-control-overview.html) permission policies\.

**Topics**
+ [Actions defined by Amazon S3](#amazons3-actions-as-permissions)
+ [Resource types defined by Amazon S3](#amazons3-resources-for-iam-policies)
+ [Condition keys for Amazon S3](#amazons3-policy-keys)

## Actions defined by Amazon S3<a name="amazons3-actions-as-permissions"></a>

You can specify the following actions in the `Action` element of an IAM policy statement\. Use policies to grant permissions to perform an operation in AWS\. When you use an action in a policy, you usually allow or deny access to the API operation or CLI command with the same name\. However, in some cases, a single action controls access to more than one operation\. Alternatively, some operations require several different actions\.

The **Resource types** column indicates whether each action supports resource\-level permissions\. If there is no value for this column, you must specify all resources \("\*"\) in the `Resource` element of your policy statement\. If the column includes a resource type, then you can specify an ARN of that type in a statement with that action\. Required resources are indicated in the table with an asterisk \(\*\)\. If you specify a resource\-level permission ARN in a statement using this action, then it must be of this type\. Some actions support multiple resource types\. If the resource type is optional \(not indicated as required\), then you can choose to use one but not the other\.


****  
[\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/list_amazons3.html)

## Resource types defined by Amazon S3<a name="amazons3-resources-for-iam-policies"></a>

The following resource types are defined by this service and can be used in the `Resource` element of IAM permission policy statements\. Each action in the [Actions table](#amazons3-actions-as-permissions) identifies the resource types that can be specified with that action\. A resource type can also define which condition keys you can include in a policy\. These keys are displayed in the last column of the table\.


****  

| Resource types | ARN | Condition keys | 
| --- | --- | --- | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/access-points.html](https://docs.aws.amazon.com/AmazonS3/latest/dev/access-points.html)  |  arn:$\{Partition\}:s3:$\{Region\}:$\{Account\}:accesspoint/$\{AccessPointName\}  |  | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingBucket.html](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingBucket.html)  |  arn:$\{Partition\}:s3:::$\{BucketName\}  |  | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingObjects.html](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingObjects.html)  |  arn:$\{Partition\}:s3:::$\{BucketName\}/$\{ObjectName\}  |  | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/batch-ops-managing-jobs.html](https://docs.aws.amazon.com/AmazonS3/latest/dev/batch-ops-managing-jobs.html)  |  arn:$\{Partition\}:s3:$\{Region\}:$\{Account\}:job/$\{JobId\}  |  | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/storage_lens.html](https://docs.aws.amazon.com/AmazonS3/latest/dev/storage_lens.html)  |  arn:$\{Partition\}:s3:$\{Region\}:$\{Account\}:storage\-lens/$\{ConfigId\}  |   [aws:ResourceTag/$\{TagKey\}](#amazons3-aws_ResourceTag___TagKey_)   | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/transforming-objects.html](https://docs.aws.amazon.com/AmazonS3/latest/dev/transforming-objects.html)  |  arn:$\{Partition\}:s3\-object\-lambda:$\{Region\}:$\{Account\}:accesspoint/$\{AccessPointName\}  |  | 

## Condition keys for Amazon S3<a name="amazons3-policy-keys"></a>

Amazon S3 defines the following condition keys that can be used in the `Condition` element of an IAM policy\. You can use these keys to further refine the conditions under which the policy statement applies\. 

To view the global condition keys that are available to all services, see [Available global condition keys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#AvailableKeys)\.


****  

| Condition keys | Description | Type | 
| --- | --- | --- | 
|   [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-requesttag](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-requesttag)  | Filters actions based on the tags that are passed in the request | String | 
|   [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-resourcetag](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-resourcetag)  | Filters actions based on the tags associated with the resource | String | 
|   [https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-tagkeys](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_condition-keys.html#condition-keys-tagkeys)  | Filters actions based on the tag keys that are passed in the request | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/creating-access-points.html#access-points-policies](https://docs.aws.amazon.com/AmazonS3/latest/dev/creating-access-points.html#access-points-policies)  | Filters access by the network origin \(Internet or VPC\) | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/creating-access-points.html#access-points-policies](https://docs.aws.amazon.com/AmazonS3/latest/dev/creating-access-points.html#access-points-policies)  | Filters access by the AWS account ID that owns the access point | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/creating-access-points.html#access-points-policies](https://docs.aws.amazon.com/AmazonS3/latest/dev/creating-access-points.html#access-points-policies)  | Filters access by an access point Amazon Resource Name \(ARN\) | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/batch-ops-job-tags-examples.html](https://docs.aws.amazon.com/AmazonS3/latest/dev/batch-ops-job-tags-examples.html)  | Filters access to updating the job priority by operation | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/batch-ops-job-tags-examples.html](https://docs.aws.amazon.com/AmazonS3/latest/dev/batch-ops-job-tags-examples.html)  | Filters access to cancelling existing jobs by priority range | Numeric | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/object-tagging.html#tagging-and-policies](https://docs.aws.amazon.com/AmazonS3/latest/dev/object-tagging.html#tagging-and-policies)  | Filters access by existing object tag key and value | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/batch-ops-job-tags-examples.html](https://docs.aws.amazon.com/AmazonS3/latest/dev/batch-ops-job-tags-examples.html)  | Filters access to cancelling suspended jobs by a specific job suspended cause \(for example, AWAITING\_CONFIRMATION\) | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/amazon-s3-policy-keys.html#condition-key-bucket-ops-1](https://docs.aws.amazon.com/AmazonS3/latest/dev/amazon-s3-policy-keys.html#condition-key-bucket-ops-1)  | Filters access by a specific Region | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/batch-ops-job-tags-examples.html](https://docs.aws.amazon.com/AmazonS3/latest/dev/batch-ops-job-tags-examples.html)  | Filters access to creating jobs by operation | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/batch-ops-job-tags-examples.html](https://docs.aws.amazon.com/AmazonS3/latest/dev/batch-ops-job-tags-examples.html)  | Filters access to creating new jobs by priority range | Numeric | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/object-tagging.html#tagging-and-policies](https://docs.aws.amazon.com/AmazonS3/latest/dev/object-tagging.html#tagging-and-policies)  | Filters access by the tag keys and values to be added to objects | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/userguide/amazon-s3-policy-keys.html#example-object-resource-account](https://docs.aws.amazon.com/AmazonS3/latest/userguide/amazon-s3-policy-keys.html#example-object-resource-account)  | Filters access by the tag keys to be added to objects | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/amazon-s3-policy-keys.html#condition-key-bucket-ops-2](https://docs.aws.amazon.com/AmazonS3/latest/dev/amazon-s3-policy-keys.html#condition-key-bucket-ops-2)  | Filters access by the resource owner AWS account ID | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/userguide/amazon-s3-policy-keys.html#example-object-tls-version](https://docs.aws.amazon.com/AmazonS3/latest/userguide/amazon-s3-policy-keys.html#example-object-tls-version)  | Filters access by the TLS version used by the client | Numeric | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/amazon-s3-policy-keys.html#getobjectversion-limit-access-to-specific-version-3](https://docs.aws.amazon.com/AmazonS3/latest/dev/amazon-s3-policy-keys.html#getobjectversion-limit-access-to-specific-version-3)  | Filters access by a specific object version | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/API/bucket-policy-s3-sigv4-conditions.html](https://docs.aws.amazon.com/AmazonS3/latest/API/bucket-policy-s3-sigv4-conditions.html)  | Filters access by authentication method | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/walkthrough1.html](https://docs.aws.amazon.com/AmazonS3/latest/dev/walkthrough1.html)  | Filters access by delimiter parameter | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/amazon-s3-policy-keys.html#condition-key-bucket-ops-1](https://docs.aws.amazon.com/AmazonS3/latest/dev/amazon-s3-policy-keys.html#condition-key-bucket-ops-1)  | Filters access by a specific Region | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/amazon-s3-policy-keys.html#example-numeric-condition-operators](https://docs.aws.amazon.com/AmazonS3/latest/dev/amazon-s3-policy-keys.html#example-numeric-condition-operators)  | Filters access by maximum number of keys returned in a ListBucket request | Numeric | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/object-lock-overview.html#object-lock-legal-holds](https://docs.aws.amazon.com/AmazonS3/latest/dev/object-lock-overview.html#object-lock-legal-holds)  | Filters access by object legal hold status | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/object-lock-overview.html#object-lock-retention-modes](https://docs.aws.amazon.com/AmazonS3/latest/dev/object-lock-overview.html#object-lock-retention-modes)  | Filters access by object retention mode \(COMPLIANCE or GOVERNANCE\) | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/object-lock-managing.html#object-lock-managing-retention-limits](https://docs.aws.amazon.com/AmazonS3/latest/dev/object-lock-managing.html#object-lock-managing-retention-limits)  | Filters access by remaining object retention days | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/object-lock-overview.html#object-lock-retention-periods](https://docs.aws.amazon.com/AmazonS3/latest/dev/object-lock-overview.html#object-lock-retention-periods)  | Filters access by object retain\-until date | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/amazon-s3-policy-keys.html#condition-key-bucket-ops-2](https://docs.aws.amazon.com/AmazonS3/latest/dev/amazon-s3-policy-keys.html#condition-key-bucket-ops-2)  | Filters access by key name prefix | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/API/bucket-policy-s3-sigv4-conditions.html](https://docs.aws.amazon.com/AmazonS3/latest/API/bucket-policy-s3-sigv4-conditions.html)  | Filters access by the age in milliseconds of the request signature | Numeric | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/API/bucket-policy-s3-sigv4-conditions.html](https://docs.aws.amazon.com/AmazonS3/latest/API/bucket-policy-s3-sigv4-conditions.html)  | Filters access by the version of AWS Signature used on the request | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/amazon-s3-policy-keys.html#getobjectversion-limit-access-to-specific-version-3](https://docs.aws.amazon.com/AmazonS3/latest/dev/amazon-s3-policy-keys.html#getobjectversion-limit-access-to-specific-version-3)  | Filters access by a specific object version | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html#permissions](https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html#permissions)  | Filters access by canned ACL in the request's x\-amz\-acl header | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/API/bucket-policy-s3-sigv4-conditions.html](https://docs.aws.amazon.com/AmazonS3/latest/API/bucket-policy-s3-sigv4-conditions.html)  | Filters access to unsigned content in your bucket | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/amazon-s3-policy-keys.html#putobject-limit-copy-source-3](https://docs.aws.amazon.com/AmazonS3/latest/dev/amazon-s3-policy-keys.html#putobject-limit-copy-source-3)  | Filters access to requests with a specific bucket, prefix, or object as the copy source | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html#permissions](https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html#permissions)  | Filters access to requests with the x\-amz\-grant\-full\-control \(full control\) header | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html#permissions](https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html#permissions)  | Filters access to requests with the x\-amz\-grant\-read \(read access\) header | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html#permissions](https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html#permissions)  | Filters access to requests with the x\-amz\-grant\-read\-acp \(read permissions for the ACL\) header | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html#permissions](https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html#permissions)  | Filters access to requests with the x\-amz\-grant\-write \(write access\) header | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html#permissions](https://docs.aws.amazon.com/AmazonS3/latest/dev/acl-overview.html#permissions)  | Filters access to requests with the x\-amz\-grant\-write\-acp \(write permissions for the ACL\) header | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html)  | Filters access by object metadata behavior \(COPY or REPLACE\) when objects are copied | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingServerSideEncryption.html](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingServerSideEncryption.html)  | Filters access by server\-side encryption | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingKMSEncryption.html#require-sse-kms](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingKMSEncryption.html#require-sse-kms)  | Filters access by AWS KMS key for server\-side encryption | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/userguide/ServerSideEncryptionCustomerKeys.html#ssec-require-condition-key](https://docs.aws.amazon.com/AmazonS3/latest/userguide/ServerSideEncryptionCustomerKeys.html#ssec-require-condition-key)  | Filters access by customer specified algorithm for server\-side encryption | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/storage-class-intro.html#sc-howtoset](https://docs.aws.amazon.com/AmazonS3/latest/dev/storage-class-intro.html#sc-howtoset)  | Filters access by storage class | String | 
|   [https://docs.aws.amazon.com/AmazonS3/latest/dev/how-to-page-redirect.html#page-redirect-using-rest-api](https://docs.aws.amazon.com/AmazonS3/latest/dev/how-to-page-redirect.html#page-redirect-using-rest-api)  | Filters access by a specific website redirect location for buckets that are configured as static websites | String | 