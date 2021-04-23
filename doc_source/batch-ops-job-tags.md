# Controlling access and labeling jobs using tags<a name="batch-ops-job-tags"></a>

You can label and control access to your S3 Batch Operations jobs by adding *tags*\. Tags can be used to identify who is responsible for a Batch Operations job\. The presence of job tags can grant or limit a user's ability to cancel a job, activate a job in the confirmation state, or change a job's priority level\. You can create jobs with tags attached to them, and you can add tags to jobs after they are created\. Each tag is a key\-value pair that can be included when you create the job or updated later\.

**Warning**  
Job tags should not contain any confidential information or personal data\.

Consider the following tagging example: Suppose that you want your Finance department to create a Batch Operations job\. You could write an AWS Identity and Access Management \(IAM\) policy that allows a user to invoke `CreateJob`, provided that the job is created with the `Department` tag assigned the value `Finance`\. Furthermore, you could attach that policy to all users who are members of the Finance department\.

Continuing with this example, you could write a policy that allows a user to update the priority of any job that has the desired tags, or cancel any job that has those tags\. For more information, see [Controlling permissions for S3 Batch Operations using job tags](batch-ops-job-tags-examples.md)\.

You can add tags to new S3 Batch Operations jobs when you create them, or you can add them to existing jobs\. 

Note the following tag restrictions:
+ You can associate up to 50 tags with a job as long as they have unique tag keys\.
+ A tag key can be up to 128 Unicode characters in length, and tag values can be up to 256 Unicode characters in length\.
+ The key and values are case sensitive\.

For more information about tag restrictions, see [User\-Defined Tag Restrictions](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/allocation-tag-restrictions.html) in the *AWS Billing and Cost Management User Guide*\.

## API operations related to S3 Batch Operations job tagging<a name="batch-ops-job-tags-api"></a>

Amazon S3 supports the following API operations that are specific to S3 Batch Operations job tagging:
+ [GetJobTagging](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetJobTagging.html) — Returns the tag set associated with a Batch Operations job\. 
+ [PutJobTagging](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_PutJobTagging.html) — Replaces the tag set associated with a job\. There are two distinct scenarios for S3 Batch Operations job tag management using this API action:
  + Job has no tags — You can add a set of tags to a job \(the job has no prior tags\)\.
  + Job has a set of existing tags — To modify the existing tag set, you can either replace the existing tag set entirely, or make changes within the existing tag set by retrieving the existing tag set using [GetJobTagging](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_GetJobTagging.html), modify that tag set, and use this API action to replace the tag set with the one you have modified\.
**Note**  
If you send this request with an empty tag set, S3 Batch Operations deletes the existing tag set on the object\. If you use this method, you are charged for a Tier 1 Request \(PUT\)\. For more information, see [Amazon S3 pricing](https://aws.amazon.com/s3/pricing)\.  
To delete existing tags for your Batch Operations job, the `DeleteJobTagging` action is preferred because it achieves the same result without incurring charges\.
+ [DeleteJobTagging](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_DeleteJobTagging.html) — Deletes the tag set associated with a Batch Operations job\. 