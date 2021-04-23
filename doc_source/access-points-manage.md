# Using Amazon S3 access points with the Amazon S3 console<a name="access-points-manage"></a>

This section explains how to manage and use your Amazon S3 access points using the AWS Management Console\. Before you begin, navigate to the detail page for the access point you want to manage or use, as described in the following procedure\.

**Topics**
+ [Listing access points for your account](#access-points-detail-page-nav)
+ [Listing access points for a bucket](#listing-access-points-bucket)
+ [Viewing configuration details for an access point](#access-points-manage-use-detail)
+ [Using an access point](#use-single-access-point)
+ [Viewing block public access settings for an access point](#view-bpa-access-point)
+ [Editing an access point policy](#edit-access-point-policy)
+ [Deleting an access point](#delete-access-point)

## Listing access points for your account<a name="access-points-detail-page-nav"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane on the left side of the console, choose **access points**\.

1. On the **access points** page, under **access points**, select the AWS Region that contains the access points you want to list\.

1. \(Optional\) Search for access points by name by entering a name into the text field next to the Region dropdown menu\.

1. Choose the name of the access point you want to manage or use\.

## Listing access points for a bucket<a name="listing-access-points-bucket"></a>

**To list all access points for a single bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the navigation pane on the left side of the console, choose **Buckets**\.

1. On the **Buckets** page, select the name of the bucket whose access points you want to list\.

1. On the bucket detail page, choose the **access points** tab\.

1. Choose the name of the access point you want to manage or use\.

## Viewing configuration details for an access point<a name="access-points-manage-use-detail"></a>

1. Navigate to the access point detail page for the access point whose details you want to view, as described in [Listing access points for your account](#access-points-detail-page-nav)\.

1. Under **access point overview**, view configuration details and properties for the selected access point\.

## Using an access point<a name="use-single-access-point"></a>

1. Navigate to the access point detail page for the access point you want to use, as described in [Listing access points for your account](#access-points-detail-page-nav)\.

1. Under the **Objects** tab, choose the name of an object or objects that you want to access through the access point\. On the object operation pages, the console displays a label above the name of your bucket that shows the access point that you're currently using\. While you're using the access point, you can only perform the object operations that are allowed by the access point permissions\.
**Note**  
The console view always shows all objects in the bucket\. Using an access point as described in this procedure restricts the operations you can perform on those objects, but not whether you can see that they exist in the bucket\.
The S3 Management Console doesn't support using virtual private cloud \(VPC\) access points to access bucket resources\. To access bucket resources from a VPC access point, use the AWS CLI, AWS SDKs, or Amazon S3 REST APIs\.

## Viewing block public access settings for an access point<a name="view-bpa-access-point"></a>

1. Navigate to the access point detail page for the access point whose settings you want to view, as described in [Listing access points for your account](#access-points-detail-page-nav)\.

1. Choose **Permissions**\.

1. Under **access point policy**, review the access point's Block Public Access settings\.
**Note**  
You can't change the Block Public Access settings for an access point after the access point is created\.

## Editing an access point policy<a name="edit-access-point-policy"></a>

1. Navigate to the access point detail page for the access point whose policy you want to edit, as described in [Listing access points for your account](#access-points-detail-page-nav)\.

1. Choose **Permissions**\.

1. Under **access point policy**, choose **Edit**\.

1. Enter the access point policy in the text field\. The console automatically displays the Amazon Resource Name \(ARN\) for the access point, which you can use in the policy\.

## Deleting an access point<a name="delete-access-point"></a>

1. Navigate to the list of access points for your account or for a specific bucket, as described in [Listing access points for your account](#access-points-detail-page-nav)\.

1. Select the option button next to the name of the access point that you want to delete\.

1. Choose **Delete**\.

1. Confirm that you want to delete your access point by entering its name in the text field that appears, and choose **Delete**\.