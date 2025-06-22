---
title: "Project 1: Cloud Resume Challenge Part 2"
date: 2025-06-07
categories: [Cloud Projects]
tags: [Cloud Resume Challenge - AWS]
---
Go back to [Part 1](https://c-kq.github.io/posts/CloudResumeChallenge-Part1/)...  
  
  
## Dynamic Website - Page Views Counter
Having successfully deployed a secure webpage using **S3, CloudFront, Route 53, and ACM**, the next phase involves implementing a dynamic page view counter. This will be achieved by integrating **JavaScript, DynamoDB, Lambda, and API Gateway**.  
I first create a simple HTML element for the JavaScript to work with.  
![image](personalAssets/Images/Projects/ResumeChallenge/7_1_JavaScript_PageViews.png)
  
After ensuring it works, I proceed to create the **DynamoDB** table. With "Item" as PK, I created a "Views" key with the value to track the number of views.  
![image](personalAssets/Images/Projects/ResumeChallenge/7_2_DynamoDB.png)
  
Next up, we have to update the table dynamically using Lambda. I created a function to increase the view count by 1 and return the JSON data.  
![image](personalAssets/Images/Projects/ResumeChallenge/7_3_Lambda Function.png)
  
To have the Lambda function work with our HTML page. I used API Gateway to be the connector between the JavaScript and the Lambda function. You can either **Add trigger** directly from the Lambda function page or create the API from API Gateway and link it.  
![image](personalAssets/Images/Projects/ResumeChallenge/7_4_API Gateway Configurations.png)
  
>**Learning Point**
After implementing the API & calling it via my website, it still wasn't working. I checked the console and realised there was an 403 error. It was **blocked by CORS Policy**. For remediation, I enabled the CORS policy for my website **only** in API Gateway to get it working.  

![image](personalAssets/Images/Projects/ResumeChallenge/7_5_LearningPoint_CORS.png)
![image](personalAssets/Images/Projects/ResumeChallenge/7_5_LearningPoint_CORSEnabled.png)
  
Additionally, since my website only needed to retrive data from the Lambda function, I changed the API to only allow GET from numViews.  
![image](personalAssets/Images/Projects/ResumeChallenge/7_6_APIGateway_Getonly.png)
  
Lastly, I edit the JavaScript to call and fetch the data from the Lambda function, and dynamincally change the values of ID _views_ according to the data retrieved.  
![image](personalAssets/Images/Projects/ResumeChallenge/7_7_JavascriptUpdate.png)
  
With that, the **View Counter** is complete.  
  
  
## Infrastructure as Code (IAC) - Terraform
This section was particularly engaging for me. As part of the reason I pursue cloud engineering is for the ability to automate infrastructure and build a more highly availability & fault tolerant system. Since I was relatively new to this area, I dedicated some time to learning before implementing IaC. One valuable resource that really enabled me in completing this task was from this [video](https://www.youtube.com/watch?v=7xngnjfIlK4) by [DevOps Directive](https://www.youtube.com/@DevOpsDirective). It provided a solid framework for structuring Terraform configuration files for AWS and also offered insights into using GitHub Actions.  
  
Once I grasped the fundamental concepts of Terraform configuration, the main task became translating my manually provisioned AWS infrastructure into code. I learned that Terraform establishes relationships and "links" between resources by referencing them using their user-defined names and arguments. This allows for clear dependencies and a streamlined infrastructure setup....  
![image](personalAssets/Images/Projects/ResumeChallenge/12_1_IAAC_DynamoDB.png)  
  
![image](personalAssets/Images/Projects/ResumeChallenge/12_2_IAAC_S3DependsOn.png)  
  
Both Terraform and AWS CloudFormation are declarative tools. This means you simply define the desired state of your infrastructure by specifying the necessary resources, their arguments, and values. The tools then intelligently determine the correct deployment sequence, executing operations in parallel where possible.  
  
While this declarative approach offers significant convenience compared to imperative methods (where you explicitly define each step), it can lead to issues. Specifically, if resources have underlying software dependencies that aren't explicitly linked within the infrastructure code itself, the automated deployment order might not align with these hidden requirements. In such cases, **Terraform's depends_on** (and **CloudFormation's DependsOn**) becomes crucial. This explicit dependency ensures that a particular resource is only deployed after another, even if no direct attribute reference exists between them, thus preventing deployment failures due to unfulfilled software prerequisites.  
  
Through the completion of this task, it allowed me to value how convenient a tool like Terraform & AWS CloudFormation could be. In mere minutes or even seconds, an entire infrastructure could be brought up, reducing the downtime and human errors.  
![image](personalAssets/Images/Projects/ResumeChallenge/12_3_IAAC_CloudFront_Deployment.png)  
_Actual time was roughly ~1 min. It took longer as Terraform was waiting for CloudFront Distribution to change its state away from _‘Deploying’_. This can be bypassed by changing **wait_for_deplyment** argument to **false**, which will be used later for github actions to reduce run time cost._  
  
>**Learning Point 1**  
Though Terraform is a great tool, a user must have a solid understanding of the cloud infrastructure that they are using. Terraform can only be effectively applied if the user knows both the benefits & limitations of the cloud provider. A common example of this is the knowledge that **S3 buckets having unique name & restrictions**. Because, I am using CloudFront as distribution rather than direct S3 hosting, my bucket name doesn't strictly need to match my domain - _but I would still like to reserve the domain name for future needs_. As such, by consulting the [Terraform documentation for AWS - S3 Bucket](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket), I discovered I can generate a unique name for my S3 bucket by using the **bucket_prefix** argument.  

![image](personalAssets/Images/Projects/ResumeChallenge/12_4_IAAC_S3BucketName.png)  
  
>**Learning Point 2**  
Additionally with IAC, we could not have the convenience that AWS provides us in the UI console. Convenient features such as having a button to immediately create and link an OAC or simply creating a Lambda API Gateway involve multiple resource configurations in Terraform. This learning experience gave me a more detailed insight & appreciation of how things work in the background of AWS.  

![image](personalAssets/Images/Projects/ResumeChallenge/12_5_IAAC_OAC&APIGateway.png)  
  
>**Learning Point 3**  
Building on **Learning Point 2**, some things might not workout as what you expect as compared to when you deployed via console. For example, I was wondering why CloudFront was delivering to me files to download when I accessing the website instead of the static webpage.  

![image](personalAssets/Images/Projects/ResumeChallenge/12_6_IAAC_S3FileType.png)  
  
>I investigated the issue by going into the S3 origin and click the **Open** button for the objects and realised the issue was from the objects being uploaded. After some research, I realised files being uploaded to S3 via Terraform must have the content type specified. Hence, I did a lookup for the file type while it was uploading and used it for the **content_type** argument.  

![image](personalAssets/Images/Projects/ResumeChallenge/12_7_IAAC_ContentTypeLookup.png)  
  
  
  
  
Continue to [Part 3](https://c-kq.github.io/posts/CloudResumeChallenge-Part3/)...

















