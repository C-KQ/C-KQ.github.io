![image](https://github.com/user-attachments/assets/278b350d-aaeb-400b-aaab-dc94fb0078d3)![image](https://github.com/user-attachments/assets/7959b80c-5c80-4898-b6c8-947dcdd41001)---
title: "Project 1: Cloud Resume Challenge"
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
  
4. Under Default cache behavior > Viewer protocol policy, select _Redirect HTTP to HTTPS_.  
>+ Force viewers to use HTTPS only.  

![image](assets/img/projects/ResumeChallenge/5_HTTPS_3_Redirect HTTP to HTTPS.png)
   
5. Leave the rest as default values, scroll down and click _Create Distribution_.  
6. Copy the S3 bucket policy, and return to the S3 bucket where your website is hosted.  
![image](assets/img/projects/ResumeChallenge/5_HTTPS_6_Copy Bucket Policy.png)
  
7. Go to Permissions > Bucket Policy and click _Edit_  
![image](assets/img/projects/ResumeChallenge/5_HTTPS_7_Edit Bucket Policy.png)
  
8. Replace the current policy with the copied policy. Then click _Save Changes_.  
![image](assets/img/projects/ResumeChallenge/5_HTTPS_8_Replace Policy.png)
  
9. Test if you are able access directly to S3 Origin. It should fail.  
![image](assets/img/projects/ResumeChallenge/5_HTTPS_9_Check S3 Origin Direct Access.png)
  
10. For security hygiene purposes, go back to the S3 bucket, and revert back step 2 & 3 under [Static Website](#4-static-website) as we do not need the public access anymore.  
![image](assets/img/projects/ResumeChallenge/5_HTTPS_10_Revert Block Public Access.png)
  
![image](assets/img/projects/ResumeChallenge/5_HTTPS_10_Revert Static Web Hosting.png)
  
10. Lastly, access the static webpage by going to the CloudFront Distribution > General > Under Details, copy the Distribution domain name.
![image](assets/img/projects/ResumeChallenge/5_HTTPS_11_CloudFront Distribution Link.png)
  
12. Enter the URL you copied, change to _http_ & add '_/index.html_' to the back.
    In this instance, it is: http://dgiybnjyd4or4.cloudfront.net/index.html.  
    Check that it should redirect you back to HTTPS. Check the cert if it is valid.  
![image](assets/img/projects/ResumeChallenge/5_HTTPS_12_Static Page via CloudFront.png)
  
## 5. DNS  
>Point a custom DNS domain name to the CloudFront distribution, so your resume can be accessed at something like my-c00l-resume-website.com. You can use Amazon Route 53 or any other DNS provider for this.  

I have registered my own domain on AWS from previous exercises. I will be using the domain to point to the CloudFront distribution.  
  
1. Head over to AWS Certificate Manager (ACM), ensure you are in N.Virginia (us-east-1) & _Request a certificate_.  
![image](assets/img/projects/ResumeChallenge/6_DNS_1_ACM Request Cert.png)
  
2. Ensure _Request a public certificate_ is selected, then click Next.  
![image](assets/img/projects/ResumeChallenge/6_DNS_2_Public Cert.png)
  
3. Under the Fully qualified domain name, enter the domain name which you wished to be qualified. In my case, I selected ckq123.click and *.ckq123.click (for future use (e.g., www)). Click _Request_ once you are done.   
![image](assets/img/projects/ResumeChallenge/6_DNS_3_Public Cert Settings.png)

4. As my domain is hosted in Route 53, I will create the CNAME records directly by clicking _Create records in Route 53_ for validation. Followed by _Create Records_.  
![image](assets/img/projects/ResumeChallenge/6_DNS_4_Create Records in Route53.png)
    
![image](assets/img/projects/ResumeChallenge/6_DNS_4_Create Records.png) 
After a few minutes, the status should change from **Pending validation** to **Issued**. Your SSL cert is created.  
![image](assets/img/projects/ResumeChallenge/6_DNS_4_Public Cert Issued.png)
  
5. Head back to the CloudFront Distribution > General and under Settings, click _Edit_  
![image](assets/img/projects/ResumeChallenge/6_DNS_5_CloudFront Edit.png)
  
6. Under Custom SSL certificate - optional, select your custom SSL certificate.  
![image](assets/img/projects/ResumeChallenge/6_DNS_6_Update SSL Cert.png)
  
7. Under **Alternate domain name (CNAME) - optional**, click _Add Item_ and type in your subdomain to be redirected to your distribution.  
8. Under **Default root object - optional** enter _index.html_. So that when viewers access the root URL, it will automatically view index.html.  
>**Learning Point**  
I realised I should have done step 8 in the [HTTPS](#5-https) segment so that I did not need to append 'index.html' in the link.

![image](assets/img/projects/ResumeChallenge/6_DNS_7_Update Alternative Domain Name & Default Root Object.png)
  
10. Go to Route 53 > Hosted Zones > Click on your domain > Click _Create Record_  
![image](assets/img/projects/ResumeChallenge/6_DNS_9_Create Hosted Zone Record.png)
  
11. Create 2 records via simple routing to your cloudfront distribution.
![image](assets/img/projects/ResumeChallenge/6_DNS_10_Create 2 Simple Record.png)

12. Test your website. In my case, I should enter:  
    - http://www.ckq123.click
    - http://ckq123.click  
    Both should return me to the static page (resume) with the correct SSL certificate.  
![image](assets/img/projects/ResumeChallenge/6_DNS_11_Test Website Access via own domain.png)
  
>**Learning Point**  
I was having an issue where both _https://ckq123.click_ & _http://ckq123.click_ was providing me with an 403 error.  
Issue was fixed as I realised I needed to add ckq132.click in CloudFront **Alternate domain name (CNAME) - optional** too.  

![image](assets/img/projects/ResumeChallenge/6_DNS_Learning Point_403 Error.png)
  
![image](assets/img/projects/ResumeChallenge/6_DNS_Learning Point_Add Alternative Domain Name.png)





to be continued...
