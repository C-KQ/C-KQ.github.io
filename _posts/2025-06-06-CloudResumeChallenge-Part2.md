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
  
