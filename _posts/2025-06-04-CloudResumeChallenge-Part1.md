---
title: "Project 1: Cloud Resume Challenge Part 1"
date: 2025-06-04
categories: [Cloud Projects]
tags: [AWS]
---

# Cloud Resume Challenge  
Hello, I have chosen the 'Cloud Resume Challenge' as my first project (like many others too). Below is the documentation of what I have done and learnt...  
Full requirements of the challenge can be found [here](https://cloudresumechallenge.dev/docs/the-challenge/aws/).
  
## 1. Certification  
>Your resume needs to have the AWS Cloud Practitioner certification on it.  

Completed both AWS Cloud Practioner and Solutions Architect certificate. They will be placed in the **Certification & Participation** section of the resume.  
  
## 2. HTML  
>Your resume needs to be written in HTML. Not a Word doc, not a PDF.  

I have used my basic understanding of HTML (& CSS) for this segment. For the initial skeleton of the resume, I have simply copy & paste the text from my current resume and used containers (div) to manage the structure of the resume.  
  
## 3. CSS  
>Your resume needs to be styled with CSS. No worries if you’re not a designer  

Purely using HTML is quite a challenge to get the proper formatting of the resume.  
With CSS, I was able to almost fully replicate my actual resume. Below are some of the functions I have used to make the resume as close as possible to my original PDF file:  
- Used % for the width & margin:auto so that the page can still be viewed nicely at different browser sizes.
- Used font-family & font-weight to match as close to original document as possible.
- Used margin and padding to adjust any 'awkward' spacing.
- Used inline-block & border-box together with text-align to display the tables.
- Used PowerPoint's color picker to color match the orignal document colours & used its HEX values when applying the background & line colours.
  
![image](assets/img/projects/ResumeChallenge/3_CSS.png)


## 4. Static Website
>Your HTML resume should be deployed online as an Amazon S3 static website.

Below are the steps I have taken to host a static website on S3.  
1. Upload the HTML and CSS files to S3.  
![image](assets/img/projects/ResumeChallenge/4_Static_1_Upload HTML & CSS.png)
  
2. Under Properties, enable static web hosting in S3 and specify the default index document. 
![image](assets/img/projects/ResumeChallenge/4_Static_2_Static Web Hosting.png)
  
3. Under Permissions, unblock public access.  
![image](assets/img/projects/ResumeChallenge/4_Static_3_Unblock Public Access.png)  
  
4. Under Permissions, set the bucket policy (remembering to change the ARN)  
   Reference link for bucket policy: [Public Bucket Policy](https://docs.aws.amazon.com/AmazonS3/latest/userguide/HostingWebsiteOnS3Setup.html#step4-add-bucket-policy-make-content-public)  
![image](assets/img/projects/ResumeChallenge/4_Static_4_Bucket Policy.png)   
  
5. Finally, access the static website by scrolling down under properties to get the bucket website endpoint.
![image](assets/img/projects/ResumeChallenge/4_Static_5_S3 Static Web Link.png) 

## 5. HTTPS  
>The S3 website URL should use HTTPS for security. You will need to use Amazon CloudFront to help with this.  

CloudFront provide some advantages such as...  
+ Deliver the content closer to the viewers, enabling faster load time.
+ Force viewers to use HTTPS only.
+ Only allowing viewers to access the content via CloudFront. Hence, disallowing direct access to the origin - in this case, it will be S3.  
  
1. Go to CloudFront and _Create Distritbution_. Choose the S3 bucket (**NOT WEBSITE ENDPOINT** as we will be using OAC) where the static website is hosted as the Origin domain.    
![image](assets/img/projects/ResumeChallenge/5_HTTPS_1_Create Distribution.png)
  
2. Under Origin access, choose _Origin access control settings (recommended)_. Then, _Create New OAC_. This step is to disallow direct access to the S3 origin.  
>+ Only allowing viewers to access the content via CloudFront. Hence, disallowing direct access to the origin - in this case, it will be S3.  
  
![image](assets/img/projects/ResumeChallenge/5_HTTPS_2_OAC.png)
  
3. Under Default cache behavior > Viewer protocol policy, select _Redirect HTTP to HTTPS_.  
>+ Force viewers to use HTTPS only.  

![image](assets/img/projects/ResumeChallenge/5_HTTPS_3_Redirect HTTP to HTTPS.png)
   
4. Leave the rest as default values, scroll down and click _Create Distribution_.  
5. Copy the S3 bucket policy, and return to the S3 bucket where your website is hosted.  
![image](assets/img/projects/ResumeChallenge/5_HTTPS_6_Copy Bucket Policy.png)
  
6. Go to Permissions > Bucket Policy and click _Edit_  
![image](assets/img/projects/ResumeChallenge/5_HTTPS_7_Edit Bucket Policy.png)
  
7. Replace the current policy with the copied policy. Then click _Save Changes_.  
![image](assets/img/projects/ResumeChallenge/5_HTTPS_8_Replace Policy.png)
  
8. Test if you are able access directly to S3 Origin. It should fail.  
![image](assets/img/projects/ResumeChallenge/5_HTTPS_9_Check S3 Origin Direct Access.png)
  
9. For security hygiene purposes, go back to the S3 bucket, and revert back step 2 & 3 under [Static Website](#4-static-website) as we do not need the public access anymore.  
![image](assets/img/projects/ResumeChallenge/5_HTTPS_10_Revert Block Public Access.png)
  
![image](assets/img/projects/ResumeChallenge/5_HTTPS_10_Revert Static Web Hosting.png)
  
10. Lastly, access the static webpage by going to the CloudFront Distribution > General > Under Details, copy the Distribution domain name.
![image](assets/img/projects/ResumeChallenge/5_HTTPS_11_CloudFront Distribution Link.png)
  
12. Enter the URL you copied, change to _http_ & add '_/index.html_' to the back.
    In this instance, it is: http://dgiybnjyd4or4.cloudfront.net/index.html.  
    Check that it should redirect you back to HTTPS. Check the cert if it is valid.  
![image](assets/img/projects/ResumeChallenge/5_HTTPS_12_Static Page via CloudFront.png)





Continue to [Part 2](https://c-kq.github.io/posts/CloudResumeChallenge-Part2/)...
