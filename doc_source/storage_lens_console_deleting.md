# Deleting an Amazon S3 Storage Lens dashboard<a name="storage_lens_console_deleting"></a>

**Note**  
 Before deleting a dashboard, consider the following:  
As an alternative to deleting a dashboard, you can *disable* the dashboard so that it is available to be re\-enabled in the future\. For more information, see [Disabling an Amazon S3 Storage Lens dashboard](storage_lens_console_disabling.md)\.
Deleting the dashboard will delete all the configuration settings that are associated with it\.
 Deleting a dashboard will make all the historic metrics data unavailable\. This historical data is still available for queries until it expires \(14 days or 15 months, depending on whether it is a free or advanced metrics dashboard\)\. If you want to access this data again, create a dashboard with the same name in the same home Region as the one that was deleted\.

**To delete an S3 Storage Lens dashboard**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane, choose **Storage Lens**, **Dashboards**\.

1. In the **Dashboards** list, choose the dashboard that you want to delete, and then choose **Delete** at the top of the list\.

1. On the **Delete dashboards** page, confirm that you want to delete the dashboard by entering the name of dashboard into the text field\. Then choose **Confirm**\. 