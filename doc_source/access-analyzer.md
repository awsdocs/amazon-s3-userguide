--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Reviewing bucket access using Access Analyzer for S3<a name="access-analyzer"></a>

Access Analyzer for S3 alerts you to S3 buckets that are configured to allow access to anyone on the internet or other AWS accounts, including AWS accounts outside of your organization\. For each public or shared bucket, you receive findings into the source and level of public or shared access\. For example, Access Analyzer for S3 might show that a bucket has read or write access provided through a bucket access control list \(ACL\), a bucket policy, or an access point policy\. Armed with this knowledge, you can take immediate and precise corrective action to restore your bucket access to what you intended\. 

When reviewing an at\-risk bucket in Access Analyzer for S3, you can block all public access to the bucket with a single click\. We recommend that you block all access to your buckets unless you require public access to support a specific use case\. Before you block all public access, ensure that your applications will continue to work correctly without public access\. For more information, see [Blocking public access to your Amazon S3 storage](access-control-block-public-access.md)\.

You can also drill down into bucket\-level permission settings to configure granular levels of access\. For specific and verified use cases that require public access, such as static website hosting, public downloads, or cross\-account sharing, you can acknowledge and record your intent for the bucket to remain public or shared by archiving the findings for the bucket\. You can revisit and modify these bucket configurations at any time\. You can also download your findings as a CSV report for auditing purposes\.

Access Analyzer for S3 is available at no extra cost on the Amazon S3 console\. Access Analyzer for S3 is powered by AWS Identity and Access Management \(IAM\) Access Analyzer\. To use Access Analyzer for S3 in the Amazon S3 console, you must visit the IAM console and enable IAM Access Analyzer on a per\-Region basis\. 

For more information about IAM Access Analyzer, see [What is Access Analyzer?](https://docs.aws.amazon.com/IAM/latest/UserGuide/what-is-access-analyzer.html) in the *IAM User Guide*\. For more information about Access Analyzer for S3, review the following sections\.

**Important**  
Access Analyzer for S3 requires an account\-level analyzer\. To use Access Analyzer for S3, you must visit IAM Access Analyzer and create an analyzer that has an account as the zone of trust\. For more information, see [Enabling Access Analyzer](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-getting-started.html#access-analyzer-enabling) in *IAM User Guide*\.
When a bucket policy or bucket ACL is added or modified, Access Analyzer generates and updates findings based on the change within 30 minutes\. Findings related to account level block public access settings may not be generated or updated for up to 6 hours after you change the settings\.

****Topics****
+ [What information does Access Analyzer for S3 provide?](#access-analyzer-information-s3)
+ [Enabling Access Analyzer for S3](#enabling-access-analyzer)
+ [Blocking all public access](#blocking-public-access-access-analyzer)
+ [Reviewing and changing bucket access](#changing-bucket-access)
+ [Archiving bucket findings](#archiving-buckets)
+ [Activating an archived bucket finding](#activating-buckets)
+ [Viewing finding details](#viewing-finding-details)
+ [Downloading an Access Analyzer for S3 report](#downloading-bucket-report-s3)

## What information does Access Analyzer for S3 provide?<a name="access-analyzer-information-s3"></a>

Access Analyzer for S3 provides findings for buckets that can be accessed outside your AWS account\. Buckets that are listed under **Buckets with public access** can be accessed by anyone on the internet\. If Access Analyzer for S3 identifies public buckets, you also see a warning at the top of the page that shows you the number of public buckets in your Region\. Buckets listed under **Buckets with access from other AWS accounts — including third\-party AWS accounts** are shared conditionally with other AWS accounts, including accounts outside of your organization\. 

For each bucket, Access Analyzer for S3 provides the following information:
+ **Bucket name**
+ **Discovered by Access analyzer** ‐ When Access Analyzer for S3 discovered the public or shared bucket access\.
+ **Shared through** ‐ How the bucket is shared—through a bucket policy, a bucket ACL, or an access point policy\. A bucket can be shared through both policies and ACLs\. If you want to find and review the source for your bucket access, you can use the information in this column as a starting point for taking immediate and precise corrective action\. 
+ **Status** ‐ The status of the bucket finding\. Access Analyzer for S3 displays findings for all public and shared buckets\. 
  + **Active **‐ Finding has not been reviewed\. 
  + **Archived** ‐ Finding has been reviewed and confirmed as intended\. 
  + **All** ‐ All findings for buckets that are public or shared with other AWS accounts, including AWS accounts outside of your organization\.
+ **Access level** ‐ Access permissions granted for the bucket:
  + **List** ‐ List resources\.
  + **Read** ‐ Read but not edit resource contents and attributes\.
  + **Write** ‐ Create, delete, or modify resources\.
  + **Permissions** ‐ Grant or modify resource permissions\.
  + **Tagging** ‐ Update tags associated with the resource\.

## Enabling Access Analyzer for S3<a name="enabling-access-analyzer"></a>

To use Access Analyzer for S3, you must complete the following prerequisite steps\.

1. Grant the required permissions\.

   For more information, see [Permissions Required to use Access Analyzer](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-getting-started.html#access-analyzer-permissions) in the *IAM User Guide*\.

1. Visit IAM to create an account\-level analyzer for each Region where you want to use Access Analyzer\.

   Access Analyzer for S3 requires an account\-level analyzer\. To use Access Analyzer for S3, you must create an analyzer that has an account as the zone of trust\. For more information, see [Enabling Access Analyzer](https://docs.aws.amazon.com/IAM/latest/UserGuide/access-analyzer-getting-started.html#access-analyzer-enabling) in *IAM User Guide*\.

## Blocking all public access<a name="blocking-public-access-access-analyzer"></a>

If you want to block all access to a bucket in a single click, you can use the **Block all public access** button in Access Analyzer for S3\. When you block all public access to a bucket, no public access is granted\. We recommend that you block all public access to your buckets unless you require public access to support a specific and verified use case\. Before you block all public access, ensure that your applications will continue to work correctly without public access\.

If you don't want to block all public access to your bucket, you can edit your block public access settings on the Amazon S3 console to configure granular levels of access to your buckets\. For more information, see [Blocking public access to your Amazon S3 storage](access-control-block-public-access.md)\.

In rare events, Access Analyzer for S3 might report no findings for a bucket that an Amazon S3 block public access evaluation reports as public\. This happens because Amazon S3 block public access reviews policies for current actions and any potential actions that might be added in the future, leading to a bucket becoming public\. On the other hand, Access Analyzer for S3 only analyzes the current actions specified for the Amazon S3 service in the evaluation of access status\.

**To block all public access to a bucket using Access Analyzer for S3**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane on the left, under **Dashboards**, choose **Access analyzer for S3**\.

1. In Access Analyzer for S3, choose a bucket\.

1. Choose **Block all public access**\.

1. To confirm your intent to block all public access to the bucket, in **Block all public access \(bucket settings\)**, enter **confirm**\.

   Amazon S3 blocks all public access to your bucket\. The status of the bucket finding updates to **resolved**, and the bucket disappears from the Access Analyzer for S3 listing\. If you want to review resolved buckets, open IAM Access Analyzer on the IAM console\.

## Reviewing and changing bucket access<a name="changing-bucket-access"></a>

If you did not intend to grant access to the public or other AWS accounts, including accounts outside of your organization, you can modify the bucket ACL, bucket policy, or access point policy to remove the access to the bucket\. The **Shared through** column shows all sources of bucket access: bucket policy, bucket ACL, and/or access point policy\.

**To review and change a bucket policy, a bucket ACL, or an access point policy**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Access analyzer for S3**\.

1. To see whether public access or shared access is granted through a bucket policy, a bucket ACL, or an access point policy, look in the **Shared through** column\.

1. Under **Buckets**, choose the name of the bucket with the bucket policy, bucket ACL, or access point policy that you want to change or review\.

1. If you want to change or view a bucket ACL:

   1. Choose **Permissions**\.

   1. Choose **Access Control List**\.

   1. Review your bucket ACL, and make changes as required\.

      For more information, see [Configuring ACLs](managing-acls.md)

1. If you want to change or review a bucket policy:

   1. Choose **Permissions**\.

   1. Choose **Bucket Policy**\.

   1. Review or change your bucket policy as required\.

      For more information, see [Adding a bucket policy using the Amazon S3 console](add-bucket-policy.md)

1. If you want to review or change an access point policy:

   1. Choose **Access points**\.

   1. Choose the access point name\.

   1. Review or change access as required\. 

      For more information, see [Using Amazon S3 access points with the Amazon S3 console](access-points-manage.md)\.

   If you edit or remove a bucket ACL, a bucket policy, or an access point policy to remove public or shared access, the status for the bucket findings updates to resolved\. The resolved bucket findings disappear from the Access Analyzer for S3 listing, but you can view them in IAM Access Analyzer\.

## Archiving bucket findings<a name="archiving-buckets"></a>

If a bucket grants access to the public or other AWS accounts, including accounts outside of your organization, to support a specific use case \(for example, a static website, public downloads, or cross\-account sharing\), you can archive the finding for the bucket\. When you archive bucket findings, you acknowledge and record your intent for the bucket to remain public or shared\. Archived bucket findings remain in your Access Analyzer for S3 listing so that you always know which buckets are public or shared\.

**To archive bucket findings in Access Analyzer for S3**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Access analyzer for S3**\.

1. In Access Analyzer for S3, choose an active bucket\.

1. To acknowledge your intent for this bucket to be accessed by the public or other AWS accounts, including accounts outside of your organization, choose **Archive**\.

1. Enter **confirm**, and choose **Archive**\.

## Activating an archived bucket finding<a name="activating-buckets"></a>

After you archive findings, you can always revisit them and change their status back to active, indicating that the bucket requires another review\. 

**To activate an archived bucket finding in Access Analyzer for S3**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Access analyzer for S3**\.

1. Choose the archived bucket findings\.

1. Choose **Mark as active**\.

## Viewing finding details<a name="viewing-finding-details"></a>

If you need to see more information about a bucket, you can open the bucket finding details in IAM Access Analyzer on the IAM console\.

**To view finding details in Access Analyzer for S3**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Access analyzer for S3**\.

1. In Access Analyzer for S3, choose a bucket\.

1. Choose **View details**\.

   The finding details open in IAM Access Analyzer on the IAM console\. 

## Downloading an Access Analyzer for S3 report<a name="downloading-bucket-report-s3"></a>

You can download your bucket findings as a CSV report that you can use for auditing purposes\. The report includes the same information that you see in Access Analyzer for S3 on the Amazon S3 console\.

**To download a report**

1. Open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane on the left, choose **Access analyzer for S3**\.

1. In the Region filter, choose the Region\.

   Access Analyzer for S3 updates to shows buckets for the chosen Region\.

1. Choose **Download report**\.

   A CSV report is generated and saved to your computer\.