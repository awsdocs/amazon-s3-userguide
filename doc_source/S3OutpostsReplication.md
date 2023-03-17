# Replicating objects for S3 on Outposts<a name="S3OutpostsReplication"></a>

With S3 Replication on AWS Outposts, you can configure Amazon S3 on Outposts to automatically replicate S3 objects across different Outposts, or between buckets on the same Outpost\. You can use S3 Replication on Outposts to maintain multiple replicas of your data in the same or different Outposts, or across different accounts, to help meet data\-residency needs\. S3 Replication on Outposts helps power your compliant storage needs and data sharing across accounts\. If you need to ensure that your replicas are identical to the source data, you can use S3 Replication on Outposts to make replicas of your objects that retain all metadata, such as the original object creation time, tags, and version IDs\.

S3 Replication on Outposts also provides detailed metrics and notifications to monitor the status of object replication between buckets\. You can use Amazon CloudWatch to monitor replication progress by tracking bytes pending replication, operations pending replication, and replication latency between your source and destination buckets\. To quickly diagnose and correct configuration issues, you can also set up Amazon EventBridge to receive notifications about replication object failures\. To learn more, see [Managing your replication](manage-outposts-replication.md)\.

**Topics**
+ [Replication configuration](#outposts-replication-add-config)
+ [Requirements for S3 Replication on Outposts](#outposts-replication-requirements)
+ [What is replicated?](#outposts-replication-what-is-replicated)
+ [What isn't replicated?](#outposts-replication-what-is-not-replicated)
+ [What isn't supported by S3 Replication on Outposts?](#outposts-replication-what-is-not-supported)
+ [Setting up replication](outposts-replication-how-setup.md)
+ [Managing your replication](manage-outposts-replication.md)

## Replication configuration<a name="outposts-replication-add-config"></a>

S3 on Outposts stores a replication configuration as XML\. In the replication configuration XML file, you specify an AWS Identity and Access Management \(IAM\) role and one or more rules\. 

```
<ReplicationConfiguration>
    <Role>IAM-role-ARN</Role>
    <Rule>
        ...
    </Rule>
    <Rule>
         ... 
    </Rule>
     ...
</ReplicationConfiguration>
```

S3 on Outposts can't replicate objects without your permission\. You grant S3 on Outposts permissions with the IAM role that you specify in the replication configuration\. S3 on Outposts assumes that IAM role to replicate objects on your behalf\. You must grant the required permissions to the IAM role before starting replication\. For more information about these permissions for S3 on Outposts, see [Creating an IAM role](outposts-replication-prerequisites-config.md#outposts-rep-pretwo)\. 

You add one rule in a replication configuration in the following scenarios:
+ You want to replicate all objects\.
+ You want to replicate one subset of objects\. You identify the object subset by adding a filter in the rule\. In the filter, you specify an object key prefix, tags, or a combination of both, to identify the subset of objects that the rule applies to\. 

You add multiple rules in a replication configuration if you want to replicate different subsets of objects\. In each rule, you specify a filter that selects a different subset of objects\. For example, you might choose to replicate objects that have either `tax/` or `document/` key prefixes\. To do this, you add two rules, one that specifies the `tax/` key prefix filter and another that specifies the `document/` key prefix\.

For more information about S3 on Outposts replication configuration and replication rules, see [ ReplicationConfiguration ](https://docs.aws.amazon.com/AmazonS3/latest/API/API_control_ReplicationConfiguration.html) in the *Amazon Simple Storage Service API Reference*\.

## Requirements for S3 Replication on Outposts<a name="outposts-replication-requirements"></a>

Replication requires the following:
+ The destination Outpost CIDR range must be associated in your source Outpost subnet table\. For more information, see [Prerequisites for creating replication rules](outposts-replication-prerequisites-config.md)\.
+ Both the source and destination buckets must have S3 Versioning enabled\. For more information about versioning, see [Managing S3 Versioning for your S3 on Outposts bucket](S3OutpostsManagingVersioning.md)\.
+ Amazon S3 on Outposts must have permission to replicate objects from the source bucket to the destination bucket on your behalf\. That means you must create a service role to delegate `GET` and `PUT` permissions to S3 on Outposts\. 

  1. Before creating the service role, you must have `GET` permission on the source bucket and `PUT` permission on the destination bucket\.

  1. To create the service role to delegate permissions to S3 on Outposts, you must first configure permissions to allow an IAM entity \(a user or role\) to perform the `iam:CreateRole` and `iam:PassRole` actions\. Then, you allow the IAM entity to create a service role\. To make S3 on Outposts assume the service role on your behalf and delegate `GET` and `PUT` permissions to S3 on Outposts, you must assign the necessary trust and permissions policies to the role\. For more information about these permissions for S3 on Outposts, see [Creating an IAM role](outposts-replication-prerequisites-config.md#outposts-rep-pretwo)\. For more information about creating a service role, see [Creating a service role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-service.html)\.

## What is replicated?<a name="outposts-replication-what-is-replicated"></a>

By default, S3 on Outposts replicates the following:
+ Objects created after you add a replication configuration\.
+ Object metadata from the source objects to the replicas\. For information about how to replicate metadata from the replicas to the source objects, see [Replication status if Amazon S3 replica modification sync on Outposts is enabled](manage-outposts-replication.md#outposts-replication-status-sync)\.
+ Object tags, if there are any\.

### How delete operations affect replication<a name="outposts-replication-delete-op"></a>

If you delete an object from the source bucket, the following actions occur by default:
+ If you make a `DELETE` request without specifying an object version ID, S3 on Outposts adds a delete marker\. S3 on Outposts deals with the delete marker as follows:
  + S3 on Outposts does not replicate the delete marker by default\.
  + However, you can add *delete marker replication* to non\-tag\-based rules\. For more information about how to enable delete marker replication in your replication configuration, see [Using the S3 console](replication-between-outposts.md#outposts-enable-replication)\.
+ If you specify an object version ID to delete in a `DELETE` request, S3 on Outposts permanently deletes that object version in the source bucket\. However, it doesn't replicate the deletion in the destination buckets\. In other words, it doesn't delete the same object version from the destination buckets\. This behavior protects data from malicious deletions\. 

## What isn't replicated?<a name="outposts-replication-what-is-not-replicated"></a>

By default, S3 on Outposts doesn't replicate the following:
+ Objects in the source bucket that are replicas that were created by another replication rule\. For example, suppose you configure replication where bucket A is the source and bucket B is the destination\. Now suppose that you add another replication configuration where bucket B is the source and bucket C is the destination\. In this case, objects in bucket B that are replicas of objects in bucket A are not replicated to bucket C\. 
+ Objects in the source bucket that have already been replicated to a different destination\. For example, if you change the destination bucket in an existing replication configuration, S3 on Outposts won't replicate the objects again\.
+ Objects that are created with server\-side encryption with customer\-provided encryption keys \(SSE\-C\)\.
+ Updates to bucket\-level subresources\. 

  For example, if you change the lifecycle configuration or add a notification configuration to your source bucket, these changes are not applied to the destination bucket\. This feature makes it possible to have different configurations on the source and destination buckets\. 
+ Actions performed by lifecycle configuration\. 

  For example, if you enable a lifecycle configuration only on your source bucket and configure expiration actions, S3 on Outposts creates delete markers for expired objects in the source bucket but doesn't replicate those markers to the destination buckets\. If you want the same lifecycle configuration applied to both the source and destination buckets, enable the same lifecycle configuration on both\. For more information about lifecycle configuration, see [Managing your storage lifecycle](object-lifecycle-mgmt.md)\.

## What isn't supported by S3 Replication on Outposts?<a name="outposts-replication-what-is-not-supported"></a>

The following S3 Replication features are currently not supported by S3 on Outposts:
+ S3 Replication Time Control \(S3 RTC\)\. S3 RTC is not supported because the object traffic in S3 Replication on Outposts travels over your on\-premises network \(the local gateway\)\. For more information about local gateways, see [ Working with the local gateway](https://docs.aws.amazon.com/outposts/latest/userguide/outposts-local-gateways.html#working-with-lgw) in the *AWS Outposts User Guide*\.
+ S3 Replication for Batch Operations\.