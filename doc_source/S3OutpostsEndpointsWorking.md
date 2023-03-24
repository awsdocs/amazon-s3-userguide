# Working with Amazon S3 on Outposts endpoints<a name="S3OutpostsEndpointsWorking"></a>

To route requests to an Amazon S3 on Outposts access point, you must create and configure an S3 on Outposts endpoint\. In order to create an endpoint, you will need an active connection with your service link to your Outposts home region\. Each virtual private cloud \(VPC\) on your Outpost can have one associated endpoint\. For more information about endpoint quotas, see [ S3 on Outposts network requirements](S3OnOutpostsRestrictionsLimitations.md#S3OnOutpostsConnectivityRestrictions)\. You must create an endpoint to be able to access your Outposts buckets and perform object operations\. For more information, see [Endpoints](S3OutpostsWorkingBuckets.md#S3OutpostsEP)\.

After you create an endpoint, you can use the ‘Status’ field, to understand the state of the endpoint\. If your Outposts is offline, it will return a CREATE\_FAILED\. You can check your service link connection, delete the endpoint, and retry the create operation after your connection has resumed\. For a list of additional error codes, see below\. For more information, see [Endpoints](S3OutpostsWorkingBuckets.md#S3OutpostsEP)\.


| API | Status | Failed Reason Error Code | Message \- Failed Reason | 
| --- | --- | --- | --- | 
| CreateEndpoint | Create\_Failed | OutpostNotReachable | Endpoint could not be created as the service link connection to your Outposts home Region is down\. Check your connection, delete the endpoint, and try again\. | 
| CreateEndpoint | Create\_Failed | InternalError | Endpoint could not be created due to Internal Error\. Please delete the endpoint and create again\. | 
| DeleteEndpoint | Delete\_Failed | OutpostNotReachable | Endpoint could not be deleted as the service link connection to your Outposts home Region is down\. Check your connection and please try again\. | 
| DeleteEndpoint | Delete\_Failed | InternalError | Endpoint could not be deleted due to Internal Error\. Please try again\. | 

For more information about working with buckets on S3 on Outposts, see [Working with S3 on Outposts buckets](S3OutpostsWorkingBuckets.md)\.

The following sections describe how to create and manage endpoints for S3 on Outposts\.

**Topics**
+ [Creating an endpoint on an Outpost](S3OutpostsCreateEndpoint.md)
+ [Viewing a list of your Amazon S3 on Outposts endpoints](S3OutpostsListEndpoints.md)
+ [Deleting an Amazon S3 on Outposts endpoint](S3OutpostsDeleteEndpoints.md)