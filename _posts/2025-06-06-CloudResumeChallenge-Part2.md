---
title: "Project 1: Cloud Resume Challenge Part 2"
date: 2025-06-07
categories: [Cloud Projects]
tags: [Cloud Resume Challenge - AWS]
---
Go back to [Part 1](https://c-kq.github.io/posts/CloudResumeChallenge-Part1/)...
   
  
## Dynamic Website - Page Views Counter
So far, we have used S3, CloudFront, Route53 & ACM to host a secure webpage. Next up, we are going to use JavaScript, DynamoDB and Lambda with API Gateway to implement a dynamic page view counter.  
I first create a simple HTML element for the JavaScript to work with.  
![image](personalAssets/Images/Projects/ResumeChallenge/7_1_JavaScript_PageViews.png)
  
After ensuring it works, I proceed to create the **DynamoDB** table. With "Item" as PK, I created a "Views" key with the value to track the number of views.  
![image](personalAssets/Images/Projects/ResumeChallenge/7_2_DynamoDB.png)
  
Next up, we have to update the table dynamically using Lambda. I created a function to increase the view count by 1 and return the JSON data.  
![image](personalAssets/Images/Projects/ResumeChallenge/7_3_Lambda Function.png)
  
To have the Lambda function work with our HTML page. I used API Gateway to be the connector between the JavaScript and the Lambda function. You can either _Add trigger_ directly from the Lambda function page or create the API from API Gateway and link it.  
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
  
  
## Infrastructure as a Code (IAAC) - Terraform
This portion was really interesting to me. As part of the reason I pursue cloud engineering is for the ability to automate infrastructure and provide high availability (& fault tolerance) for systems. As I was still relatively new to this topic, I did some studying before diving into it. One awesome resource that really enabled me in completing this task was from this [video](https://www.youtube.com/watch?v=7xngnjfIlK4) by [DevOps Directive](https://www.youtube.com/@DevOpsDirective). It gave me the guideline on how to code a terraform configuration file for AWS and some insights in github actions too.  
  
Once I got the general idea of how the configuration file works, it was a matter of matching the config files' output result to what I produce manually. With Terraform, I learnt that the method to 'linking' and creating a relationship between the resources was by calling the resource with their user-defined name and arguments...  
![image](personalAssets/Images/Projects/ResumeChallenge/12_1_IAAC_DynamoDB.png)  
  
or by using 'depends_on'...  
![image](personalAssets/Images/Projects/ResumeChallenge/12_2_IAAC_S3DependsOn.png)  
  
Both Terraform & AWS CloudFomation are declarative tools. Meaning, by just providing the proper resources, arguments & values, they will figure out the correct sequence of which resources to deploy first (or in parallel). Though this provides a greater convenience as compared to an imperative tool, things that are not linked in nature but are linked due to software dependencies might cause some issues when deploying. As such, Terraform's _depends_on_ (similar to AWS CloudFormation DependsOn) is especially important.  
  
Through the completion of this task, it allowed me to value how convenient a tool like Terraform & AWS CloudFormation could be. In mere minutes or even seconds, an entire infrastructure could be brought up, reducing the downtime and human errors.  
![image](personalAssets/Images/Projects/ResumeChallenge/12_3_IAAC_CloudFront_Deployment.png)  
_Actual time was roughly ~1 min. It took longer as Terraform was waiting for CloudFront Distribution to change its state away from _‘Deploying’_. This can be negated by changing **wait_for_deplyment** argument to **false**, which will be used later for github actions to reduce run time cost._  
  
>**Learning Point 1**  
Though Terraform is a great tool, a user must have certain knowledge of the cloud infrastructure that they are using. Terraform can only be effectively applied if the user knows both the benefits & limitations of the cloud provider. A simple example will be the common knowledge of S3 buckets having unique name & restrictions. As I am using CloudFront as distribution and not S3, I do not need my bucket name to be the same as my domain - _but I would still like to reserve the domain name for future needs_. As such, by reading through the documentation in [Terraform for AWS - S3 Bucket](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/s3_bucket), I can generate a unique name for my S3 bucket by using the **bucket_prefix** argument.  

![image](personalAssets/Images/Projects/ResumeChallenge/12_4_IAAC_S3BucketName.png)  
  
>**Learning Point 2**  
Additionally with IAAC, we could not have the convenience that AWS provides us in the UI console. Convenient features such as having a button to immediately create and link an OAC or simply creating a Lambda API Gateway involve multiple resource configurations in Terraform. This learning experience gave me a more detailed insight & appreciation of how things work in the background of AWS.  

![image](personalAssets/Images/Projects/ResumeChallenge/12_5_IAAC_OAC&APIGateway.png)  
  
>**Learning Point 3**  
Adding on to **Learning Point 2**, some things might not workout as what you expect as compared to when you deployed via console. For example, I was wondering why CloudFront was delivering to me files to download when I accessing the website instead of the static webpage.  
  
![image](personalAssets/Images/Projects/ResumeChallenge/12_6_IAAC_S3FileType.png)  
  
>I investigated the issue by going into the S3 origin and click the **Open** button for the objects and realised the issue was from the objects being uploaded. After some research, I realised files being uploaded to S3 via Terraform must have the content type specified. Hence, I did a lookup for the file type while it was uploading and used it for the **content_type** argument.  
  
![image](personalAssets/Images/Projects/ResumeChallenge/12_7_IAAC_ContentTypeLookup.png)  
  
  
  
  
Continue to [Part 3](https://c-kq.github.io/posts/CloudResumeChallenge-Part3/)...

















