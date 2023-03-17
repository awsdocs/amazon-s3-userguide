# Rules for choosing buckets for Amazon S3 Multi\-Region Access Points<a name="multi-region-access-point-buckets"></a>

Each Multi\-Region Access Point is associated with the Regions where you want to fulfill requests\. The Multi\-Region Access Point must be associated with exactly one bucket in each of those Regions\. You specify the name of each bucket in the request to create the Multi\-Region Access Point\. Buckets that support the Multi\-Region Access Point can either be in the same AWS account that owns the Multi\-Region Access Point, or they can be in other AWS accounts\.

 A single bucket can be used by multiple Multi\-Region Access Points\. 

**Important**  
You can specify the buckets that are associated with a Multi\-Region Access Point only at the time that you create it\. After it is created, you can’t add, modify, or remove buckets from the Multi\-Region Access Point configuration\. To change the buckets, you must delete the entire Multi\-Region Access Point and create a new one\. 
You can't delete a bucket that is part of a Multi\-Region Access Point\. If you want to delete a bucket that's attached to a Multi\-Region Access Point, delete the Multi\-Region Access Point first\. 
If you add a bucket that's owned by another account to your Multi\-Region Access Point, the bucket owner must also update their bucket policy to grant access permissions to the Multi\-Region Access Point\. Otherwise, the Multi\-Region Access Point won't be able to retrieve data from that bucket\. For example policies that show how to grant such access, see [Multi\-Region Access Point policy examples](https://docs.aws.amazon.com/AmazonS3/latest/userguide/MultiRegionAccessPointPermissions.html#MultiRegionAccessPointPolicyExamples)\. 
 Not all Regions support Multi\-Region Access Points\. To see the list of supported Regions, see [Multi\-Region Access Point restrictions and limitations](MultiRegionAccessPointRestrictions.md)\. 

You can create replication rules to synchronize data between buckets\. These rules enable you to automatically copy data from source buckets to destination buckets\. Having buckets connected to a Multi\-Region Access Point does not affect how replication works\. Configuring replication with Multi\-Region Access Points is described in a later section\.

**Important**  
When you make a request to a Multi\-Region Access Point, the Multi\-Region Access Point isn't aware of the data contents of the buckets in the Multi\-Region Access Point\. Therefore, the bucket that gets the request might not contain the requested data\. To create consistent datasets in the Amazon S3 buckets that are associated with a Multi\-Region Access Point, we recommend that you configure S3 Cross\-Region Replication \(CRR\)\. For more information, see [Configuring replication for use with Multi\-Region Access Points](MultiRegionAccessPointBucketReplication.md)\.