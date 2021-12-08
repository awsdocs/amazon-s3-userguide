# Amazon S3 Intelligent\-Tiering<a name="intelligent-tiering"></a>

The S3 Intelligent\-Tiering storage class is designed to optimize storage costs by automatically moving data to the most cost\-effective access tier when access patterns change, without operational overhead or impact on performance\. For a small monthly object monitoring and automation charge, S3 Intelligent\-Tiering monitors access patterns and automatically moves objects that have not been accessed to lower\-cost access tiers\.

S3 Intelligent\-Tiering delivers automatic storage cost savings in three low latency and high throughput access tiers\. For data that can be accessed asynchronously, you can choose to activate automatic archiving capabilities within the S3 Intelligent\-Tiering storage class\. There are no retrieval charges in S3 Intelligent\-Tiering\. If an object in the Infrequent Access tier or Archive Instant Access tier is accessed later, it is automatically moved back to the Frequent Access tier\. No additional tiering charges apply when objects are moved between access tiers within the S3 Intelligent\-Tiering storage class\.

For information about using S3 Intelligent\-Tiering, see the following sections:

**Topics**
+ [How S3 Intelligent\-Tiering works](intelligent-tiering-overview.md)
+ [Using S3 Intelligent\-Tiering](using-intelligent-tiering.md)
+ [Managing S3 Intelligent\-Tiering](intelligent-tiering-managing.md)