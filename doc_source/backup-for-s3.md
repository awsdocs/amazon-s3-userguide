# Using AWS Backup for Amazon S3 \(Preview\)<a name="backup-for-s3"></a>


|  | 
| --- |
| AWS Backup currently offers a public preview of its support of Amazon S3 in the US West \(Oregon\)\. If you would like to participate in the Preview, review [  Creating Amazon S3 backups \(Preview\)](https://docs.aws.amazon.com/aws-backup/latest/devguide/s3-backups.html) in the AWS Backup Developer Guide\. | 

Amazon S3 is natively integrated with AWS Backup, a fully managed, policy\-based service that you can use to define a central backup policy to protect your Amazon S3 data\. Along with AWS Backup, you can use S3 Versioning and Amazon S3 Replication to help recover from accidental deletions and perform your own self\-recovery operations\. After you define your backup policies and assign Amazon S3 resources to the policies, AWS Backup automates the creation of Amazon S3 backups\. It also stores the backups in an encrypted storage vault that you designate\.

AWS Backup cannot be applied to data protected using client\-side encryption\.

To create an Amazon S3 backup with AWS Backup, see [  Creating Amazon S3 backups \(Preview\)](https://docs.aws.amazon.com/aws-backup/latest/devguide/s3-backups.html) in the *AWS Backup Developer Guide*\.

**Highlights**

You can perform the following actions when using AWS Backup for Amazon S3:
+ Create nearly continuous point\-in\-time and periodic backups\.
+ Automate backup scheduling and retention by centrally configuring backup policies\.
+ Restore backups of Amazon S3 data to a specific point in time\.

**Note**  
You must activate [S3 Versioning](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Versioning.html) on your bucket before AWS Backup can back it up\. AWS Backup recommends that you [ set a lifecycle expiration rule for versioning\-enabled buckets](https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-configuration-examples.html#lifecycle-config-conceptual-ex6)\. If you do not set a lifecycle expiration period, your Amazon S3 storage costs might increase because AWS Backup will retain all versions of your Amazon S3 data\. 