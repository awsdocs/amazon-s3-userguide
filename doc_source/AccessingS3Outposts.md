--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Accessing Amazon S3 on Outposts using virtual private cloud \(VPC\) only access points<a name="AccessingS3Outposts"></a>

Amazon S3 on Outposts supports virtual private cloud \(VPC\)\-only access points as the only means to access Outposts buckets\. S3 on Outposts endpoints enable you to privately connect your VPC to your AWS Outposts bucket without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect connection\. 

Instances in your VPC don't require public IP addresses to communicate with resources in your Outposts\. This keeps traffic between your VPC and your S3 on Outposts buckets within the Amazon network\. 

S3 on Outposts endpoints are virtual devices\. They are horizontally scaled, redundant, and highly available VPC components\. They allow communication between instances in your VPC and S3 on Outposts without imposing availability risks or bandwidth constraints on your network traffic\.

**Note**  
To access your S3 on Outposts buckets and objects, you must have the following:  
An access point that is created for the VPC\.
An endpoint that exists for the same VPC\.

**Topics**
+ [Managing connections for S3 on Outposts using cross\-account elastic network interfaces](#S3OutpostsXENI)
+ [Permissions required for endpoints](#S3OutpostsClusters)
+ [Encryption options with S3 on Outposts](#S3OutpostsEncryption)

## Managing connections for S3 on Outposts using cross\-account elastic network interfaces<a name="S3OutpostsXENI"></a>

S3 on Outposts endpoints are named resources with proper Amazon Resource Names \(ARNs\)\. When these endpoints are created, AWS Outposts sets up four cross\-account elastic network interfaces \(*X\-ENI*\)\. X\-ENI are like other elastic network interfaces with one exception: S3 on Outposts attaches the X\-ENI to instances that it runs in the service account and has a presence in your VPC\. S3 on Outposts DNS load balances your requests over the X\-ENI\. S3 on Outposts creates the X\-ENI in your account that is visible from the elastic network interface console\.

## Permissions required for endpoints<a name="S3OutpostsClusters"></a>

To attach the cross\-account elastic network interfaces \(X\-ENI\) to cluster accounts, S3 on Outposts must also modify these network interfaces to use them with the account ID of the cluster account\. Because of CIDR restrictions, each elastic network interface is unique and on a unique IP\. The source VPC for the IP and ENI ID is recorded and is associated with the cluster ID\. 

## Encryption options with S3 on Outposts<a name="S3OutpostsEncryption"></a>

 By default, all data stored in S3 on Outposts is encrypted using server\-side encryption with Amazon S3 managed encryption keys \(SSE\-S3\)\. You can optionally use server\-side encryption with customer\-provided encryption keys \(SSE\-C\) by specifying an encryption key as part of your object API requests\. Server\-side encryption encrypts only the object data, not object metadata\. 