# Tutorial: Transforming data for your application with S3 Object Lambda<a name="tutorial-s3-object-lambda-uppercase"></a>

When you store data in Amazon S3, you can easily share it for use by multiple applications\. However, each application might have unique data format requirements, and might need modification or processing of your data for a specific use case\. For example, a dataset created by an ecommerce application might include personally identifiable information \(PII\)\. When the same data is processed for analytics, this PII is not needed and should be redacted\. However, if the same dataset is used for a marketing campaign, you might need to enrich the data with additional details, such as information from the customer loyalty database\.

With [S3 Object Lambda](http://aws.amazon.com/s3/features/object-lambda), you can add your own code to process data retrieved from S3 before returning it to an application\. Specifically, you can configure an AWS Lambda function and attach it to an S3 Object Lambda access point\. When an application sends [standard S3 GET requests](https://docs.aws.amazon.com/AmazonS3/latest/API/API_GetObject.html) through the S3 Object Lambda access point, the specified Lambda function is invoked to process any data retrieved from an S3 bucket through the supporting S3 access point\. Then, the S3 Object Lambda access point returns the transformed result back to the application\. You can author and execute your own custom Lambda functions, tailoring the S3 Object Lambda data transformation to your specific use case, all with no changes required to your applications\.

![\[This is an S3 Object Lambda workflow diagram.\]](http://docs.aws.amazon.com/AmazonS3/latest/userguide/images/ol-example-image-global.png)

**Objective**  
In this tutorial, you learn how to add custom code to standard S3 GET requests to modify the requested object retrieved from S3 so that the object suit the needs of the requesting client or application\. Specifically, you learn how to transform all the text in the original object stored in S3 to uppercase through S3 Object Lambda\. 

**Topics**
+ [Prerequisites](#ol-upper-prerequisites)
+ [Step 1: Create an S3 bucket](#ol-upper-step1)
+ [Step 2: Upload a file to the S3 bucket](#ol-upper-step2)
+ [Step 3: Create an S3 access point](#ol-upper-step3)
+ [Step 4: Create a Lambda function](#ol-upper-step4)
+ [Step 5: Configure an IAM policy for your Lambda function's execution role](#ol-upper-step5)
+ [Step 6: Create an S3 Object Lambda access point](#ol-upper-step6)
+ [Step 7: View the transformed data](#ol-upper-step7)
+ [Step 8: Clean up](#ol-upper-step8)
+ [Next steps](#ol-upper-next-steps)

## Prerequisites<a name="ol-upper-prerequisites"></a>

Before you start this tutorial, you must have an AWS account that you can sign in to as an AWS Identity and Access Management \(IAM\) user with correct permissions\. You also must install Python version 3\.8 or later\.

**Topics**
+ [Create an IAM user with permissions in your AWS account \(console\)](#ol-upper-prerequisites-account)
+ [Install Python 3\.8 or later on your local machine](#ol-upper-prerequisites-python)

### Create an IAM user with permissions in your AWS account \(console\)<a name="ol-upper-prerequisites-account"></a>

You can create an IAM user for the tutorial, or you can add permissions to an existing IAM user\. To complete this tutorial, your IAM user must attach the following IAM policies to access relevant AWS resources and perform specific actions\. 

Your IAM user requires the following policies:
+ [AmazonS3FullAccess](https://console.aws.amazon.com/iam/home?#/policies/arn:aws:iam::aws:policy/AmazonS3FullAccess$jsonEditor) – Grants permissions to all Amazon S3 actions, including permissions to create and use an Object Lambda access point\. 
+ [AWSLambda\_FullAccess](https://console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/AWSLambda_FullAccess$jsonEditor) – Grants permissions to all Lambda actions\. 
+ [IAMFullAccess](https://console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/IAMFullAccess$jsonEditor) – Grants permissions to all IAM actions\. 
+ [IAMAccessAnalyzerReadOnlyAccess](https://console.aws.amazon.com/iam/home#/policies/arn:aws:iam::aws:policy/IAMAccessAnalyzerReadOnlyAccess$jsonEditor) – Grants permissions to read all access information provided by IAM Access Analyzer\. 

**Note**  
For simplicity, this tutorial uses full\-access AWS managed policies\. For production use, we recommend that you instead grant only the minimum permissions necessary for your use case, in accordance with [security best practices](security-best-practices.md#security-best-practices-prevent)\.  
For more information about how to create an IAM user, see [Creating IAM users \(console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html#id_users_create_console) in the *IAM User Guide*\.

### Install Python 3\.8 or later on your local machine<a name="ol-upper-prerequisites-python"></a>

Use the following procedure to install Python 3\.8 or later on your local machine\. For more installation instructions, see the [Downloading Python](https://wiki.python.org/moin/BeginnersGuide/Download) page in the *Python Beginners Guide*\.

1. Open your local terminal or shell and run the following command to determine whether Python is already installed, and if so, which version is installed\. 

   ```
   python --version
   ```

1. If you don't have Python 3\.8 or later, download the [official installer](https://www.python.org/downloads/) of Python 3\.8 or later that's suitable for your local machine\. 

1. Run the installer by double\-clicking the downloaded file, and follow the steps to complete the installation\. 

   For **Windows users**, choose **Add Python 3\.X to PATH** in the installation wizard before choosing **Install Now**\. 

1. Restart your terminal by closing and reopening it\. 

1. Run the following command to verify that Python 3\.8 or later is installed correctly\. 

   For **macOS users**, run this command: 

   ```
   python3 --version
   ```

   For **Windows users**, run this command: 

   ```
   python --version
   ```

1. Run the following command to verify that the pip3 package manager is installed\. If you see a pip version number and python 3\.8 or later in the command response, that means the pip3 package manager is installed successfully\.

   ```
   pip --version
   ```

## Step 1: Create an S3 bucket<a name="ol-upper-step1"></a>

Create a bucket to store the original data that you plan to transform\. 

**To create a bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. Choose **Create bucket**\. 

   The **Create bucket** page opens\.

1. For **Bucket name**, enter a name \(for example, **tutorial\-bucket**\) for your bucket\. 

   For more information about naming buckets in Amazon S3, see [Bucket naming rules](bucketnamingrules.md)\.

1. For **Region**, choose the AWS Region where you want the bucket to reside\. 

   For more information about the bucket Region, see [Buckets overview](UsingBucket.md)\.

1. For **Block Public Access settings for this bucket**, keep the default settings \(**Block *all *public access** is enabled\)\. 

   We recommend that you keep all Block Public Access settings enabled unless you need to turn off one or more of them for your use case\. For more information about blocking public access, see [Blocking public access to your Amazon S3 storage](access-control-block-public-access.md)\.

1. For the remaining settings, keep the defaults\. 

   \(Optional\) If you want to configure additional bucket settings for your specific use case, see [Creating a bucket](create-bucket-overview.md)\.

1. Choose **Create bucket**\.

## Step 2: Upload a file to the S3 bucket<a name="ol-upper-step2"></a>

Upload a text file to the S3 bucket\. This text file contains the original data that you will transform to uppercase later in this tutorial\. 

For example, you can upload a `tutorial.txt` file that contains the following text:

```
Amazon S3 Object Lambda Tutorial:
You can add your own code to process data retrieved from S3 before 
returning it to an application.
```

**To upload a file to a bucket**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. In the **Buckets** list, choose the name of the bucket that you created in [Step 1](#ol-upper-step1) \(for example, **tutorial\-bucket**\) to upload your file to\.

1. On the **Objects** tab for your bucket, choose **Upload**\.

1. On the **Upload** page, under **Files and folders**, choose **Add files**\.

1. Choose a file to upload, and then choose **Open**\. For example, you can upload the `tutorial.txt` file example mentioned earlier\.

1. Choose **Upload**\.

## Step 3: Create an S3 access point<a name="ol-upper-step3"></a>

To use an S3 Object Lambda access point to access and transform the original data, you must create an S3 access point and associate it with the S3 bucket that you created in [Step 1](#ol-upper-step1)\. The access point must be in the same AWS Region as the objects that you want to transform\.

Later in this tutorial, you'll use this access point as a supporting access point for your Object Lambda access point\. 

**To create an access point**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Access Points**\.

1. On the **Access Points** page, choose **Create access point**\.

1. In the **Access point name** field, enter the name \(for example, **tutorial\-access\-point**\) for the access point\.

   For more information about naming access points, see [Rules for naming Amazon S3 access points](creating-access-points.md#access-points-names)\.

1. In the **Bucket name** field, enter the name of the bucket that you created in [Step 1](#ol-upper-step1) \(for example, **tutorial\-bucket**\)\. S3 attaches the access point to this bucket\. 

   \(Optional\) You can choose **Browse S3** to browse and search the buckets in your account\. If you choose **Browse S3**, choose the desired bucket, and then choose **Choose path** to populate the **Bucket name** field with that bucket's name\.

1. For **Network origin**, choose **Internet**\. 

   For more information about network origins for access points, see [Creating access points restricted to a virtual private cloud](access-points-vpc.md)\.

1. By default, all Block Public Access settings are turned on for your access point\. We recommend that you keep **Block *all* public access** enabled\.

   For more information, see [Managing public access to access points](access-points-bpa-settings.md)\.

1. For all other access point settings, keep the default settings\.

   \(Optional\) You can modify the access point settings to support your use case\. For this tutorial, we recommend keeping the default settings\. 

   \(Optional\) If you need to manage access to your access point, you can specify an access point policy\. For more information, see [Access point policy examples](access-points-policies.md#access-points-policy-examples)\. 

1. Choose **Create access point**\.

## Step 4: Create a Lambda function<a name="ol-upper-step4"></a>

To transform original data, create a Lambda function for use with your S3 Object Lambda access point\. 

**Topics**
+ [Write Lambda function code and create a deployment package with a virtual environment](#ol-upper-step4-write-lambda)
+ [Create a Lambda function with an execution role \(console\)](#ol-upper-step4-create-function)
+ [Deploy your Lambda function code with \.zip file archives and configure the Lambda function \(console\)](#ol-upper-step4-deploy-function)

### Write Lambda function code and create a deployment package with a virtual environment<a name="ol-upper-step4-write-lambda"></a>

1. On your local machine, create a folder with the folder name `object-lambda` for the virtual environment to use later in this tutorial\.

1. In the `object-lambda` folder, create a file with a Lambda function that changes all text in the original object to uppercase\. For example, you can use the following function written in Python\. Save this function in a file named `transform.py`\. 

   ```
   import boto3
   import requests
   
   # This function capitalizes all text in the original object
   def lambda_handler(event, context):
       object_context = event["getObjectContext"]
       # Get the presigned URL to fetch the requested original object 
       # from S3
       s3_url = object_context["inputS3Url"]
       # Extract the route and request token from the input context
       request_route = object_context["outputRoute"]
       request_token = object_context["outputToken"]
       
       # Get the original S3 object using the presigned URL
       response = requests.get(s3_url)
       original_object = response.content.decode("utf-8")
   
       # Transform all text in the original object to uppercase
       # You can replace it with your custom code based on your use case
       transformed_object = original_object.upper()
   
       # Write object back to S3 Object Lambda
       s3 = boto3.client('s3')
       # The WriteGetObjectResponse API sends the transformed data
       # back to S3 Object Lambda and then to the user
       s3.write_get_object_response(
           Body=transformed_object,
           RequestRoute=request_route,
           RequestToken=request_token)
   
       # Exit the Lambda function: return the status code  
       return {'status_code': 200}
   ```
**Note**  
The preceding example Lambda function loads the entire requested object into memory before transforming it and returning it to the client\. Alternatively, you can stream the object from S3 to avoid loading the entire object into memory\. This approach can be useful when working with large objects\. For more information about streaming responses with Object Lambda access points, see the streaming examples in [Working with WriteGetObjectResponse](olap-writing-lambda.md#olap-getobject-response)\.

   When you're writing a Lambda function for use with an S3 Object Lambda access point, the function is based on the input event context that S3 Object Lambda provides to the Lambda function\. The event context provides information about the request being made in the event passed from S3 Object Lambda to Lambda\. It contains the parameters that you use to create the Lambda function\.

   The fields used to create the preceding Lambda function are as follows: 

   The field of `getObjectContext` means the input and output details for connections to Amazon S3 and S3 Object Lambda\. It has the following fields:
   + `inputS3Url` – A presigned URL that the Lambda function can use to download the original object from the supporting access point\. By using a presigned URL, the Lambda function doesn't need to have Amazon S3 read permissions to retrieve the original object and can only access the object processed by each invocation\.
   + `outputRoute` – A routing token that is added to the S3 Object Lambda URL when the Lambda function calls `WriteGetObjectResponse` to send back the transformed object\.
   + `outputToken` – A token used by S3 Object Lambda to match the `WriteGetObjectResponse` call with the original caller when sending back the transformed object\.

   For more information about all the fields in the event context, see [Event context format and usage](olap-writing-lambda.md#olap-event-context) and [Writing and debugging Lambda functions for S3 Object Lambda Access Points](olap-writing-lambda.md)\.

1. In your local terminal, enter the following command to install the `virtualenv` package:

   ```
   python -m pip install virtualenv
   ```

1. In your local terminal, open the `object-lambda` folder that you created earlier, and then enter the following command to create and initialize a virtual environment called `venv`\.

   ```
   python -m virtualenv venv
   ```

1. To activate the virtual environment, enter the following command to execute the `activate` file from the environment's folder:

   For **macOS users**, run this command:

   ```
   source venv/bin/activate
   ```

   For **Windows users**, run this command:

   ```
   .\venv\Scripts\activate
   ```

   Now, your command prompt changes to show **\(venv\)**, indicating that the virtual environment is active\.

1. To install the required libraries, run the following commands line by line in the `venv` virtual environment\.

   These commands install updated versions of the dependencies of your `lambda_handler` Lambda function\. These dependencies are the AWS SDK for Python \(Boto3\) and the requests module\.

   ```
   pip3 install boto3
   ```

   ```
   pip3 install requests
   ```

1. To deactivate the virtual environment, run the following command:

   ```
   deactivate
   ```

1. To create a deployment package with the installed libraries as a `.zip` file named `lambda.zip` at the root of the `object-lambda` directory, run the following commands line by line in your local terminal\.
**Tip**  
The following commands might need to be adjusted to work in your particular environment\. For example, a library might appear in `site-packages` or `dist-packages`, and the first folder might be `lib` or `lib64`\. Also, the `python` folder might be named with a different Python version\. To locate a specific package, use the `pip show` command\.

   For **macOS users**, run these commands:

   ```
   cd venv/lib/python3.8/site-packages 
   ```

   ```
   zip -r ../../../../lambda.zip .
   ```

   For **Windows users**, run these commands:

   ```
   cd .\venv\Lib\site-packages\ 
   ```

   ```
   powershell Compress-Archive * ../../../lambda.zip
   ```

   The last command saves the deployment package to the root of the `object-lambda` directory\.

1. Add the function code file `transform.py` to the root of your deployment package\.

   For **macOS users**, run these commands:

   ```
   cd ../../../../ 
   ```

   ```
   zip -g lambda.zip transform.py
   ```

   For **Windows users**, run these commands: 

   ```
   cd ..\..\..\
   ```

   ```
   powershell Compress-Archive -update transform.py lambda.zip
   ```

   After you complete this step, you should have the following directory structure:

   ```
   lambda.zip$
     │ transform.py
     │ __pycache__
     | boto3/
     │ certifi/
     │ pip/
     │ requests/
     ...
   ```

### Create a Lambda function with an execution role \(console\)<a name="ol-upper-step4-create-function"></a>

1. Sign in to the AWS Management Console and open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

   

1. In the left navigation pane, choose **Functions**\.

1. Choose **Create function**\.

1. Choose **Author from scratch**\.

1. Under **Basic information**, do the following:

   1. For **Function name**, enter **tutorial\-object\-lambda\-function**\.

   1. For **Runtime**, choose **Python 3\.8** or a later version\.

1. Expand the **Change default execution role** section\. Under **Execution role**, choose **Create a new role with basic Lambda permissions**\.

   In [Step 5](#ol-upper-step5) later in this tutorial, you attach the **AmazonS3ObjectLambdaExecutionRolePolicy** to this Lambda function's execution role\. 

1. Keep the remaining settings set to the defaults\.

1. Choose **Create function**\.

### Deploy your Lambda function code with \.zip file archives and configure the Lambda function \(console\)<a name="ol-upper-step4-deploy-function"></a>

1. In the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/), choose **Functions** in the left navigation pane\. 

1. Choose the the Lambda function that you created earlier \(for example, **tutorial\-object\-lambda\-function**\)\. 

1. On the Lambda function's details page, choose the **Code** tab\. In the **Code Source** section, choose **Upload from** and then **\.zip file**\.

1. Choose **Upload** to select your local `.zip` file\.

1. Choose the `lambda.zip` file that you created earlier, and then choose **Open**\.

1. Choose **Save**\.

1. In the **Runtime settings** section, choose **Edit**\. 

1. On the **Edit runtime settings** page, confirm that **Runtime** is set to **Python 3\.8** or a later version\. 

1. To tell the Lambda runtime which handler method in your Lambda function code to invoke, enter **transform\.lambda\_handler** for **Handler**\.

   When you configure a function in Python, the value of the handler setting is the file name and the name of the handler module, separated by a dot\. For example, `transform.lambda_handler` calls the `lambda_handler` method defined in the `transform.py` file\.

1. Choose **Save**\.

1. \(Optional\) On your Lambda function's details page, choose the **Configuration** tab\. In the left navigation pane, choose **General configuration**, then choose **Edit**\. In the **Timeout** field, enter **1** min **0** sec\. Keep the remaining settings set to the defaults, and choose **Save**\.

   **Timeout** is the amount of time that Lambda allows a function to run for an invocation before stopping it\. The default is 3 seconds\. The maximum duration for a Lambda function used by S3 Object Lambda is 60 seconds\. Pricing is based on the amount of memory configured and the amount of time that your code runs\.

## Step 5: Configure an IAM policy for your Lambda function's execution role<a name="ol-upper-step5"></a>

To enable your Lambda function to provide customized data and response headers to the `GetObject` caller, your Lambda function's execution role must have IAM permissions to call the `WriteGetObjectResponse` API\.

**To attach an IAM policy to your Lambda function role**



1. In the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/), choose **Functions** in the left navigation pane\. 

1. Choose the function that you created in [Step 4](#ol-upper-step4) \(for example, **tutorial\-object\-lambda\-function**\)\.

1. On your Lambda function's details page, choose the **Configuration** tab, and then choose **Permissions** in the left navigation pane\. 

1. Under **Execution role**, choose the link of the **Role name**\. The IAM console opens\. 

1. On the IAM console's **Summary** page for your Lambda function's execution role, choose the **Permissions** tab, and then choose **Attach policies**\.

1. On the **Attach Permissions** page, enter **AmazonS3ObjectLambdaExecutionRolePolicy** in the search box to filter the list of policies\. Select the check box next to the name of the **AmazonS3ObjectLambdaExecutionRolePolicy** policy\. 

1. Choose **Attach policy**\. 

## Step 6: Create an S3 Object Lambda access point<a name="ol-upper-step6"></a>

An S3 Object Lambda access point provides the flexibility to invoke a Lambda function directly from an S3 GET request so that the function can process data retrieved from an S3 access point\. When creating and configuring an S3 Object Lambda access point, you must specify the Lambda function to invoke and provide the event context in JSON format as custom parameters for Lambda to use\.

**To create an S3 Object Lambda access point**

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Object Lambda Access Points**\.

1. On the **Object Lambda Access Points** page, choose **Create Object Lambda Access Point**\.

1. For **Object Lambda Access Point name**, enter the name that you want to use for the Object Lambda access point \(for example, **tutorial\-object\-lambda\-accesspoint**\)\. 

1. For **Supporting Access Point**, enter or browse to the standard access point that you created in [Step 3](#ol-upper-step3) \(for example, **tutorial\-access\-point**\), and then choose **Choose supporting Access Point**\. 

1. For **Invoke Lambda function**, you can choose either of the following two options for this tutorial\. 
   + Choose **Choose from functions in your account**, and then choose the Lambda function that you created in [Step 4](#ol-upper-step4) \(for example, **tutorial\-object\-lambda\-function**\) from the **Lambda function** dropdown list\.
   + Choose **Enter ARN**, and then enter the Amazon Resource Name \(ARN\) of the Lambda function that you created in [Step 4](#ol-upper-step4)\.

1. For **Lambda function version**, choose **$LATEST** \(the latest version of the Lambda function that you created in [Step 4](#ol-upper-step4)\)\.

1. \(Optional\) If you need your Lambda function to recognize and process GET requests with range and part number headers, select **Lambda function supports requests using range** and **Lambda function supports requests using part numbers**\. Otherwise, clear these two check boxes\.

   For more information about how to use range or part numbers with S3 Object Lambda, see [Working with Range and partNumber headers](olap-writing-lambda.md#range-get-olap)\.

1. \(Optional\) Under **Payload \- *optional***, add JSON text to provide your Lambda function with additional information\.

   A payload is optional JSON text that you can provide to your Lambda function as input for all invocations coming from a specific S3 Object Lambda access point\. To customize the behaviors for multiple Object Lambda access points that invoke the same Lambda function, you can configure payloads with different parameters, thereby extending the flexibility of your Lambda function\.

   For more information about payload, see [Event context format and usage](olap-writing-lambda.md#olap-event-context)\.

1. \(Optional\) For **Request metrics \- *optional***, choose **Disable** or **Enable** to add Amazon S3 monitoring to your Object Lambda access point\. Request metrics are billed at the standard Amazon CloudWatch rate\. For more information, see [CloudWatch pricing](http://aws.amazon.com/cloudwatch/pricing/)\.

1. Under **Object Lambda Access Point policy \- *optional***, keep the default setting\. 

   \(Optional\) You can set a resource policy\. This resource policy grants the `GetObject` API permission to use the specified Object Lambda access point\.

1. Keep the remaining settings set to the defaults, and choose **Create Object Lambda Access Point**\.

## Step 7: View the transformed data<a name="ol-upper-step7"></a>

Now, S3 Object Lambda is ready to transform your data for your use case\. In this tutorial, S3 Object Lambda transforms all the text in your object to uppercase\.

**Topics**
+ [View the transformed data in your S3 Object Lambda access point](#ol-upper-step7-check-data)
+ [Run a Python script to print the original and transformed data](#ol-upper-step7-python-print)

### View the transformed data in your S3 Object Lambda access point<a name="ol-upper-step7-check-data"></a>

When you request to retrieve a file through your S3 Object Lambda access point, you make a `GetObject` API call to S3 Object Lambda\. S3 Object Lambda invokes the Lambda function to transform your data, and then returns the transformed data as the response to the standard S3 `GetObject` API call\.

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Object Lambda Access Points**\.

1. On the **Object Lambda Access Points** page, choose the S3 Object Lambda access point that you created in [Step 6](#ol-upper-step6) \(for example, **tutorial\-object\-lambda\-accesspoint**\)\.

1. On the **Objects** tab of your S3 Object Lambda access point, select the file that has the same name \(for example, `tutorial.txt`\) as the one that you uploaded to the S3 bucket in [Step 2](#ol-upper-step2)\. 

   This file should contain all the transformed data\.

1. To view the transformed data, choose **Open** or **Download**\.

### Run a Python script to print the original and transformed data<a name="ol-upper-step7-python-print"></a>

You can use S3 Object Lambda with your existing applications\. To do so, update your application configuration to use the new S3 Object Lambda access point ARN that you created in [Step 6](#ol-upper-step6) to retrieve data from S3\.

The following example Python script prints both the original data from the S3 bucket and the transformed data from the S3 Object Lambda access point\. 

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Object Lambda Access Points**\.

1. On the **Object Lambda Access Points** page, choose the radio button to the left of the S3 Object Lambda access point that you created in [Step 6](#ol-upper-step6) \(for example, **tutorial\-object\-lambda\-accesspoint**\)\.

1. Choose **Copy ARN**\.

1. Save the ARN for use later\.

1. Write a Python script on your local machine to print both the original data \(for example, `tutorial.txt`\) from your S3 Bucket and the transformed data \(for example, `tutorial.txt`\) from your S3 Object Lambda access point\)\. You can use the following example script\. 

   ```
   import boto3
   
   s3 = boto3.client('s3')
   
   def getObject(bucket, key):
       objectBody = s3.get_object(Bucket = bucket, Key = key)
       print(objectBody["Body"].read().decode("utf-8"))
       print("\n")
   
   print('Original object from the S3 bucket:')
   # Replace the two input parameters of getObject() below with 
   # the S3 bucket name that you created in Step 1 and 
   # the name of the file that you uploaded to the S3 bucket in Step 2
   getObject("tutorial-bucket", 
             "tutorial.txt")
   
   print('Object transformed by S3 Object Lambda:')
   # Replace the two input parameters of getObject() below with 
   # the ARN of your S3 Object Lambda access point that you saved earlier and
   # the name of the file with the transformed data (which in this case is
   # the same as the name of the file that you uploaded to the S3 bucket 
   # in Step 2)
   getObject("arn:aws:s3-object-lambda:us-west-2:111122223333:accesspoint/tutorial-object-lambda-accesspoint",
             "tutorial.txt")
   ```

1. Save your Python script with a custom name \(for example, `tutorial_print.py` \) in the folder \(for example, `object-lambda`\) that you created in [Step 4](#ol-upper-step4) on your local machine\.

1. In your local terminal, run the following command from the root of the directory \(for example, `object-lambda`\) that you created in [Step 4](#ol-upper-step4)\.

   ```
   python3 tutorial_print.py
   ```

   You should see both the original data and the transformed data \(all text as uppercase\) through the terminal\. For example, you should see something like the following text\.

   ```
   Original object from the S3 bucket:
   Amazon S3 Object Lambda Tutorial:
   You can add your own code to process data retrieved from S3 before 
   returning it to an application.
   
   Object transformed by S3 Object Lambda:
   AMAZON S3 OBJECT LAMBDA TUTORIAL:
   YOU CAN ADD YOUR OWN CODE TO PROCESS DATA RETRIEVED FROM S3 BEFORE 
   RETURNING IT TO AN APPLICATION.
   ```

## Step 8: Clean up<a name="ol-upper-step8"></a>

If you transformed your data through S3 Object Lambda only as a learning exercise, delete the AWS resources that you allocated so that you no longer accrue charges\. 

**Topics**
+ [Delete the Object Lambda access point](#ol-upper-step8-delete-olap)
+ [Delete the S3 access point](#ol-upper-step8-delete-ap)
+ [Delete the execution role for your Lambda function](#ol-upper-step8-delete-lambda-role)
+ [Delete the Lambda function](#ol-upper-step8-delete-lambda-function)
+ [Delete the CloudWatch log group](#ol-upper-step8-delete-cloudwatch)
+ [Delete the original file in the S3 source bucket](#ol-upper-step8-delete-file)
+ [Delete the S3 source bucket](#ol-upper-step8-delete-bucket)
+ [Delete the IAM user](#ol-upper-step8-delete-user)

### Delete the Object Lambda access point<a name="ol-upper-step8-delete-olap"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Object Lambda Access Points**\.

1. On the **Object Lambda Access Points** page, choose the radio button to the left of the S3 Object Lambda access point that you created in [Step 6](#ol-upper-step6) \(for example, **tutorial\-object\-lambda\-accesspoint**\)\.

1. Choose **Delete**\.

1. Confirm that you want to delete your Object Lambda access point by entering its name in the text field that appears, and then choose **Delete**\.

### Delete the S3 access point<a name="ol-upper-step8-delete-ap"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Access Points**\.

1. Navigate to the access point that you created in [Step 3](#ol-upper-step3) \(for example, **tutorial\-access\-point**\), and choose the radio button next to the name of the access point\.

1. Choose **Delete**\.

1. Confirm that you want to delete your access point by entering its name in the text field that appears, and then choose **Delete**\.

### Delete the execution role for your Lambda function<a name="ol-upper-step8-delete-lambda-role"></a>

1. Sign in to the AWS Management Console and open the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/)\.

1. In the left navigation pane, choose **Functions**\.

1. Choose the function that you created in [Step 4](#ol-upper-step4) \(for example, **tutorial\-object\-lambda\-function**\)\.

1. On your Lambda function's details page, choose the **Configuration** tab, and then choose **Permissions** in the left navigation pane\. 

1. Under **Execution role**, choose the link of the **Role name**\. The IAM console opens\.

1. On the IAM console's **Summary** page of your Lambda function's execution role, choose **Delete role**\.

1. In the **Delete role** dialog box, choose **Yes, delete**\.

### Delete the Lambda function<a name="ol-upper-step8-delete-lambda-function"></a>

1. In the AWS Lambda console at [https://console\.aws\.amazon\.com/lambda/](https://console.aws.amazon.com/lambda/), choose **Functions** in the left navigation pane\. 

1. Select the check box to the left of the name of the function that you created in [Step 4](#ol-upper-step4) \(for example, **tutorial\-object\-lambda\-function**\)\.

1. Choose **Actions**, and then choose **Delete**\.

1. In the **Delete function** dialog box, choose **Delete**\.

### Delete the CloudWatch log group<a name="ol-upper-step8-delete-cloudwatch"></a>

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the left navigation pane, choose **Log groups**\.

1. Find the log group whose name ends with the Lambda function that you created in [Step 4](#ol-upper-step4) \(for example, **tutorial\-object\-lambda\-function**\)\.

1. Select the check box to the left of the name of the log group\.

1. Choose **Actions**, and then choose **Delete log group\(s\)**\.

1. In the **Delete log group\(s\)** dialog box, choose **Delete**\.

### Delete the original file in the S3 source bucket<a name="ol-upper-step8-delete-file"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. In the **Bucket name** list, choose the name of the bucket that you uploaded the original file to in [Step 2](#ol-upper-step2) \(for example, **tutorial\-bucket**\)\.

1. Select the check box to the left of the name of the object that you want to delete \(for example, `tutorial.txt`\)\.

1. Choose **Delete**\.

1. On the **Delete objects** page, in the **Permanently delete objects?** section, confirm that you want to delete this object by entering **permanently delete** in the text box\.

1. Choose **Delete objects**\.

### Delete the S3 source bucket<a name="ol-upper-step8-delete-bucket"></a>

1. Sign in to the AWS Management Console and open the Amazon S3 console at [https://console\.aws\.amazon\.com/s3/](https://console.aws.amazon.com/s3/)\.

1. In the left navigation pane, choose **Buckets**\.

1. In the **Buckets** list, choose the radio button next to the name of the bucket that you created in [Step 1](#ol-upper-step1) \(for example, **tutorial\-bucket**\)\.

1. Choose **Delete**\.

1. On the **Delete bucket** page, confirm that you want to delete the bucket by entering the bucket name in the text field, and then choose **Delete bucket**\.

### Delete the IAM user<a name="ol-upper-step8-delete-user"></a>

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the left navigation pane, choose **Users**, and then select the check box next to the user name that you want to delete\.

1. At the top of the page, choose **Delete**\.

1. In the **Delete *user name*?** dialog box, enter the user name in the text input field to confirm the deletion of the user\. Choose **Delete**\.

## Next steps<a name="ol-upper-next-steps"></a>

After completing this tutorial, you can customize the Lambda function for your use case to modify the data returned by standard S3 GET requests\.

The following is a list of common use cases for S3 Object Lambda:
+ Masking sensitive data for security and compliance\.

  For more information, see [Tutorial: Detecting and redacting PII data with S3 Object Lambda and Amazon Comprehend](tutorial-s3-object-lambda-redact-pii.md)\.
+ Filtering certain rows of data to deliver specific information\.
+ Augmenting data with information from other services or databases\.
+ Converting across data formats, such as converting XML to JSON for application compatibility\.
+ Compressing or decompressing files as they are being downloaded\.
+ Resizing and watermarking images\.
+ Implementing custom authorization rules to access data\.

For more information about S3 Object Lambda, see [Transforming objects with S3 Object Lambda](transforming-objects.md)\.