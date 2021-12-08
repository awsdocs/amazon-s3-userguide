# Data protection in Amazon S3<a name="DataDurability"></a>

Amazon S3 provides a highly durable storage infrastructure designed for mission\-critical and primary data storage\. S3 Standard, S3 Intelligent\-Tiering, S3 Standard\-IA, S3 Glacier Instant Retrieval, S3 Glacier Flexible Retrieval, and S3 Glacier Deep Archive redundantly store objects on multiple devices across a minimum of three Availability Zones in an AWS Region\. An Availability Zone is one or more discrete data centers with redundant power, networking, and connectivity in an AWS Region\. Availability Zones are physically separated by a meaningful distance, many kilometers, from any other Availability Zone, although all are within 100 km \(60 miles\) of each other\. The S3 One Zone\-IA storage class stores data redundantly across multiple devices within a single Availability Zone\. These services are designed to sustain concurrent device failures by quickly detecting and repairing any lost redundancy, and they also regularly verify the integrity of your data using checksums\.

Amazon S3 standard storage offers the following features: 
+ Backed with the [Amazon S3 Service Level Agreement\.](https://aws.amazon.com/s3/sla/)
+ Designed to provide 99\.999999999% durability and 99\.99% availability of objects over a given year\.
+ S3 Standard, S3 Intelligent\-Tiering, S3 Standard\-IA, S3 Glacier Instant Retrieval, S3 Glacier Flexible Retrieval, and S3 Glacier Deep Archive are all designed to sustain data in the event of the loss of an entire Amazon S3 Availability Zone\.

Amazon S3 further protects your data using versioning\. You can use versioning to preserve, retrieve, and restore every version of every object that is stored in your Amazon S3 bucket\. With versioning, you can easily recover from both unintended user actions and application failures\. By default, requests retrieve the most recently written version\. You can retrieve older versions of an object by specifying a version of the object in a request\. 

For data protection purposes, we recommend that you protect AWS account credentials and set up individual user accounts with AWS Identity and Access Management, so that each user is given only the permissions necessary to fulfill their job duties\.

If you require FIPS 140\-2 validated cryptographic modules when accessing AWS through a command line interface or an API, use a FIPS endpoint\. For more information about the available FIPS endpoints, see [Federal Information Processing Standard \(FIPS\) 140\-2](http://aws.amazon.com/compliance/fips/)\.

The following security best practices also address data protection in Amazon S3:
+ [Implement server-side encryption](security-best-practices.md#server-side)
+ [Enforce encryption of data in transit](security-best-practices.md#transit)
+ [Consider using Macie with Amazon S3](security-best-practices.md#macie)
+ [Identify and audit all your Amazon S3 buckets](security-best-practices.md#audit)
+ [Monitor Amazon Web Services security advisories](security-best-practices.md#advisories)