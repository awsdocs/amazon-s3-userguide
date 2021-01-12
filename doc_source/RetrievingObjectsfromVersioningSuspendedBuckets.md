--------

Welcome to the new **Amazon S3 User Guide**\! The Amazon S3 User Guide combines information and instructions from the three retired guides: *Amazon S3 Developer Guide*, *Amazon S3 Console User Guide*, and *Amazon S3 Getting Started Guide*\.

--------

# Retrieving objects from versioning\-suspended buckets<a name="RetrievingObjectsfromVersioningSuspendedBuckets"></a>

A `GET Object` request returns the current version of an object whether you've enabled versioning on a bucket or not\. The following figure shows how a simple `GET` returns the current version of an object\.

![\[Image NOT FOUND\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/versioning_GET_suspended.png)