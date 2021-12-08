# Amazon EventBridge mapping and troubleshooting<a name="ev-mapping-troubleshooting"></a>

The following table describes how Amazon S3 event types are mapped to Amazon EventBridge event types\.


|  S3 event type |  Amazon EventBridge detail type  | 
| --- | --- | 
|  [ObjectCreated:Put](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObject.html) [ObjectCreated:Post](https://docs.aws.amazon.com/AmazonS3/latest/API/RESTObjectPOST.html) [ObjectCreated:Copy](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CopyObject.html) [ObjectCreated:CompleteMultipartUpload](https://docs.aws.amazon.com/AmazonS3/latest/API/API_CompleteMultipartUpload.html)  |  Object Created  | 
|  ObjectRemoved:Delete ObjectRemoved:DeleteMarkerCreated LifecycleExpiration:Delete LifecycleExpiration:DeleteMarkerCreated  |  Object Deleted  | 
|  [ObjectRestore:Post](https://docs.aws.amazon.com/AmazonS3/latest/API/API_RestoreObject.html)  |  Object Restore Initiated  | 
|  ObjectRestore:Completed  |  Object Restore Completed  | 
|  ObjectRestore:Delete  |  Object Restore Expired  | 
|  LifecycleTransition  |  Object Storage Class Changed  | 
|  IntelligentTiering  |  Object Access Tier Changed  | 
|  [ObjectTagging:Put](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectTagging.html)  |  Object Tags Added  | 
|  [ObjectTagging:Delete](https://docs.aws.amazon.com/AmazonS3/latest/API/API_DeleteObjectTagging.html)  |  Object Tags Deleted  | 
|  [ObjectAcl:Put](https://docs.aws.amazon.com/AmazonS3/latest/API/API_PutObjectAcl.html)  |  Object ACL Updated  | 

## Amazon EventBridge troubleshooting<a name="ev-troubleshooting"></a>

For information about how to troubleshoot EventBridge, see [Troubleshooting Amazon EventBridge](https://docs.aws.amazon.com/eventbridge/latest/userguide/eb-troubleshooting.html) in the *Amazon EventBridge User Guide*\.