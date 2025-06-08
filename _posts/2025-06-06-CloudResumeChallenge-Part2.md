---
title: "Project 1: Cloud Resume Challenge Part 2"
date: 2025-06-07
categories: [Cloud Projects]
tags: [Cloud Resume Challenge - AWS]
---
Continue to [Part 1](https://c-kq.github.io/posts/CloudResumeChallenge-Part1/)...
   
  
## Dynamic Website - Page View Counter
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
After implementing the API & calling it via my website, it still wasn't working. I checked the console and realised there was an 403 error. It was **blocked by CORS Policy**. For remediation, I enabled the CORS policy for my website only in API Gateway to get it working.  

![image](personalAssets/Images/Projects/ResumeChallenge/7_5_LearningPoint_CORS.png)
![image](personalAssets/Images/Projects/ResumeChallenge/7_5_LearningPoint_CORSEnabled.png)
  
Additionally, since my website only needed to retrive data from the Lambda function, I changed the API to only alloww GET from numViews.  
![image](personalAssets/Images/Projects/ResumeChallenge/7_6_APIGateway_Getonly.png)
  
Lastly, I edit the JavaScript to call and fetch the data from the Lambda function, and dynamincally change the values of _pageViews_ according to the data retrieved.  
![image](personalAssets/Images/Projects/ResumeChallenge/7_7_JavascriptUpdate.png)
  
With that, the **View Counter** is complete.  
  
  
## Infrastructure as a Code (IAAC)  
  
in progress...




