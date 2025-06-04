---
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
  
<img width="500" height="700" alt="screenshot of resume using HTML & CSS" src="https://github.com/user-attachments/assets/57a416da-8ad7-4cdc-b261-2ec19c3db578">  

## 4. Static Website
>Your HTML resume should be deployed online as an Amazon S3 static website.

Below are the steps I have taken to host a static website on S3.  
1. Upload the HTML and CSS files to S3.
<img width="800" height="300" alt="Screenshot of files uploaded" src="https://github.com/user-attachments/assets/bb9ab80b-9a5f-4221-9a90-31a8206fafb4">  
  
2. Under Properties, enable static web hosting in S3 and specify the default index document. 
<img width="800" height="300" alt="screenshot of enablement of static web hosting on S3" src="https://github.com/user-attachments/assets/d754d1ca-c66a-4991-ae34-ca6e4ca901e6">  
  
3. Under Permissions, unblock public access.
<img width="800" height="300" alt="screenshot of allowing public access" src="https://github.com/user-attachments/assets/7e17ae4f-bf3f-4d28-8812-c16717655229">  
  
4. Under Permissions, set the bucket policy (remembering to change the ARN)  
   Reference link for bucket policy: [Public Bucket Policy](https://docs.aws.amazon.com/AmazonS3/latest/userguide/HostingWebsiteOnS3Setup.html#step4-add-bucket-policy-make-content-public)  
<img width="400" height="500" alt="screenshot of bucket policy" src="https://github.com/user-attachments/assets/6373efc4-fe53-4f38-af79-3ac78ab3c6f6">  
  
5. Finally, access the static website by scrolling down under properties to get the bucket website endpoint.
<img width="800" height="300" alt="screenshot of bucket website endpoint" src="https://github.com/user-attachments/assets/e8d5a960-8490-4506-b13f-ed535a10b8d6">

## 5. HTTPS  
>The S3 website URL should use HTTPS for security. You will need to use Amazon CloudFront to help with this.  

CloudFront provide some advantages such as...  
+ Deliver the content closer to the viewers, enabling faster load time.
+ Force viewers to use HTTPS only.
+ Only allowing viewers to access the content via CloudFront. Hence, disallowing direct access to the origin - in this case, it will be S3.  
  
1. Go to CloudFront and _Create Distritbution_. Choose the S3 bucket (**NOT WEBSITE ENDPOINT** as we will be using OAC) where the static website is hosted as the Origin domain.    
![image](https://github.com/user-attachments/assets/8f49fda7-745f-448d-b658-7a7ac179551e)
  
2. Under Origin access, choose _Origin access control settings (recommended)_. Then, _Create New OAC_. This step is to disallow direct access to the S3 origin.  
>+ Only allowing viewers to access the content via CloudFront. Hence, disallowing direct access to the origin - in this case, it will be S3.  

![image](https://github.com/user-attachments/assets/a807a45f-5c91-40d4-b000-ccb81c314ece)
  
4. Under Default cache behavior > Viewer protocol policy, select _Redirect HTTP to HTTPS_.  
>+ Force viewers to use HTTPS only.  

![image](https://github.com/user-attachments/assets/6314c31e-a071-4a99-9b64-fc05ba79cf8b)
   
5. Leave the rest as default values, scroll down and click _Create Distribution_.  
6. Copy the S3 bucket policy, and return to the S3 bucket where your website is hosted.  
![image](https://github.com/user-attachments/assets/d7ad929e-8d55-400f-a4c1-3488d02a39cc)
  
7. Go to Permissions > Bucket Policy and click _Edit_  
![image](https://github.com/user-attachments/assets/9c533e5c-c444-42fd-a8c1-ecd03bf75d25)
  
8. Replace the current policy with the copied policy. Then click _Save Changes_.  
![image](https://github.com/user-attachments/assets/c6832895-9bf1-42d8-9154-6177fee50711)
  
9. Test if you are able access directly to S3 Origin. It should fail.  
![image](https://github.com/user-attachments/assets/60ae6e56-bc75-4953-8aba-00074a162155)
  
10. For security hygiene purposes, go back to the S3 bucket, and revert back step 2 & 3 under [Static Website](#4-static-website) as we do not need the public access anymore.  
![image](https://github.com/user-attachments/assets/43b098aa-d130-467b-8b72-78eac77a4a2c)
  
![image](https://github.com/user-attachments/assets/8ee8a4e5-fc57-41d7-8620-e5c0e7984078)
  
10. Lastly, access the static webpage by going to the CloudFront Distribution > General > Under Details, copy the Distribution domain name.
![image](https://github.com/user-attachments/assets/910c9a27-6359-479b-935a-13929c85eb0f)
  
12. Enter the URL you copied, change to _http_ & add '_/index.html_' to the back.
    In this instance, it is: http://dgiybnjyd4or4.cloudfront.net/index.html.  
    Check that it should redirect you back to HTTPS. Check the cert if it is valid.  
![image](https://github.com/user-attachments/assets/50f37a8e-7449-4efd-8a28-90a73e8c5433)
  
## 5. DNS  
>Point a custom DNS domain name to the CloudFront distribution, so your resume can be accessed at something like my-c00l-resume-website.com. You can use Amazon Route 53 or any other DNS provider for this.  

I have registered my own domain on AWS from previous exercises. I will be using the domain to point to the CloudFront distribution.  
  
1. Head over to AWS Certificate Manager (ACM), ensure you are in N.Virginia (us-east-1) & _Request a certificate_.  
![image](https://github.com/user-attachments/assets/e83459e2-8acb-4fb3-9c4d-39eb7343fb9b)
  
2. Ensure _Request a public certificate_ is selected, then click Next.  
![image](https://github.com/user-attachments/assets/bd63a781-8b68-4953-9a58-5f4ef8139c8a)
  
3. Under the Fully qualified domain name, enter the domain name which you wished to be qualified. In my case, I selected ckq123.click and *.ckq123.click (for future use (e.g., www)). Click _Request_ once you are done.   
![image](https://github.com/user-attachments/assets/e8e2fbbe-104f-462a-9c00-df5705498c94)

4. As my domain is hosted in Route 53, I will create the CNAME records directly by clicking _Create records in Route 53_ for validation. Followed by _Create Records_.  
![image](https://github.com/user-attachments/assets/58e3f9a9-120f-484f-8727-740527fdc655)
    
![image](https://github.com/user-attachments/assets/97c1b973-0c42-46a7-81cf-bbd7ab8e77f9)  
After a few minutes, the status should change from **Pending validation** to **Issued**. Your SSL cert is created.  
![image](https://github.com/user-attachments/assets/f13b56e8-3022-4595-8fd5-9b4496749183)
  
5. Head back to the CloudFront Distribution > General and under Settings, click _Edit_  
![image](https://github.com/user-attachments/assets/a79ba226-da22-45a5-942f-4b9f04782794)
  
6. Under Custom SSL certificate - optional, select your custom SSL certificate.  
![image](https://github.com/user-attachments/assets/340f68f8-461f-4999-aaea-2d09165adcf7)
  
7. Under **Alternate domain name (CNAME) - optional**, click _Add Item_ and type in your subdomain to be redirected to your distribution.  
8. Under **Default root object - optional** enter _index.html_. So that when viewers access the root URL, it will automatically view index.html.  
>**Learning Point**  
I realised I should have done step 8 in the [HTTPS](#5-https) segment so that I did not need to append 'index.html' in the link.

![image](https://github.com/user-attachments/assets/b8e3e202-72d5-488d-8ed0-5ba97c82d5dc)
  
10. Go to Route 53 > Hosted Zones > Click on your domain > Click _Create Record_  
![image](https://github.com/user-attachments/assets/52da8c14-76c6-452b-91ca-0759d5c02fdc)
  
11. Create 2 records via simple routing to your cloudfront distribution.
![image](https://github.com/user-attachments/assets/b81583fd-3061-4a18-a64f-a07421fc2c7f)

12. Test your website. In my case, I should enter:  
    - http://www.ckq123.click
    - http://ckq123.click  
    Both should return me to the static page (resume) with the correct SSL certificate.  
![image](https://github.com/user-attachments/assets/47b6cccd-1a36-46ed-bbed-24342bc7cee0)
  
>**Learning Point**  
I was having an issue where both _https://ckq123.click_ & _http://ckq123.click_ was providing me with an 403 error.  
Issue was fixed as I realised I needed to add ckq132.click in CloudFront **Alternate domain name (CNAME) - optional** too.  

![image](https://github.com/user-attachments/assets/97fcd8cf-4e2c-4233-a095-ec697593e84f)
  
![image](https://github.com/user-attachments/assets/5f5fe261-6a17-4b9a-9050-e2b5257419ba)





to be continued...
