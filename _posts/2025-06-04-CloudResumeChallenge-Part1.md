---
title: "Project 1: Cloud Resume Challenge Part 1"
date: 2025-06-04
categories: [Cloud Projects]
tags: [AWS]
---

# Cloud Resume Challenge  
Hello, I have chosen the 'Cloud Resume Challenge' as my first project (like many others too). Below is the documentation of what I have done and learnt...  
Full requirements of the challenge can be found [here](https://cloudresumechallenge.dev/docs/the-challenge/aws/).
  
  
## Stage 1: Creating a Static Website
For the static page, I have used containers(div) and CSS to make the page format as close as possible to the structure of my original resume. I have used the following functions to get the format & color to near perfect:
- Used % for the width & margin:auto so that the page can still be viewed nicely at different browser sizes.
- Used font-family & font-weight to match as close to original document as possible.
- Used margin and padding to adjust any 'awkward' spacing.
- Used inline-block & border-box together with text-align to display the tables.
- Used PowerPoint's color picker to color match the orignal document colours & used its HEX values when applying the background & line colours.
  
![image](personalAssets/Images/Projects/ResumeChallenge/3_CSS.png)
  
  
Next, I have used S3 to store all the HTML, CSS & JavaScript(later) files.  
For a temporary solution, I enabled static website hosting on S3 to check if all is running well. The steps for static hosting for S3 are listed below...
  
1. Under Properties, enable static web hosting in S3 and specify the default index document. 
![image](personalAssets/Images/Projects/ResumeChallenge/4_Static_2_Static Web Hosting.png)
  
2. Under Permissions, unblock public access.  
![image](personalAssets/Images/Projects/ResumeChallenge/4_Static_3_Unblock Public Access.png)  
  
3. Under Permissions, set the bucket policy (remembering to change the ARN)  
   Reference link for bucket policy: [Public Bucket Policy](https://docs.aws.amazon.com/AmazonS3/latest/userguide/HostingWebsiteOnS3Setup.html#step4-add-bucket-policy-make-content-public)  
![image](personalAssets/Images/Projects/ResumeChallenge/4_Static_4_Bucket Policy.png)   
  
4. Finally, access the static website by scrolling down under properties to get the bucket website endpoint.
![image](personalAssets/Images/Projects/ResumeChallenge/4_Static_5_S3 Static Web Link.png) 


## Stage 2: Securing the Website (HTTPS)  
_*Side note: I will be using my own domain that I have previously created on AWS._  
  
For this segment I have used Amazon CloudFront together with AWS Certificate Manager (for public SSL certificate). CloudFront provide some advantages such as...  
+ Deliver the content closer to the viewers, enabling faster load time.
+ Force viewers to use HTTPS only.
+ Only allowing viewers to access the content via CloudFront. Hence, disallowing direct access to the origin - in this case, it will be S3.
  
  
### AWS Certificate Manager (ACM) 
Firstly, we need to create our own public SSL certificate.   
  
1. Head over to AWS Certificate Manager (ACM), ensure you are in N.Virginia (us-east-1) & _Request a certificate_.  
![image](personalAssets/Images/Projects/ResumeChallenge/6_DNS_1_ACM Request Cert.png)
  
2. Ensure _Request a public certificate_ is selected, then click Next.  
![image](personalAssets/Images/Projects/ResumeChallenge/6_DNS_2_Public Cert.png)
  
3. Under the Fully qualified domain name, enter the domain name which you wished to be qualified. In my case, I selected ckq123.click and *.ckq123.click (for future use (e.g., www)). Click _Request_ once you are done.   
![image](personalAssets/Images/Projects/ResumeChallenge/6_DNS_3_Public Cert Settings.png)

4. As my domain is hosted in Route 53, I will create the CNAME records directly by clicking _Create records in Route 53_ for validation. Followed by _Create Records_.  
![image](personalAssets/Images/Projects/ResumeChallenge/6_DNS_4_Create Records in Route53.png)
    
![image](personalAssets/Images/Projects/ResumeChallenge/6_DNS_4_Create Records.png) 
After a few minutes, the status should change from **Pending validation** to **Issued**. Your SSL cert is created.  
![image](personalAssets/Images/Projects/ResumeChallenge/6_DNS_4_Public Cert Issued.png)
  
  
### Amazon CloudFront  
As we are using CloudFront to host the static website, head over to CloudFront to create a distribution and set the origin to the S3 bucket (**NOT WEBSITE ENDPOINT**) that we previously used. There are a few options we need to edit while creating the distribution. They are...  
1. Under Origin access, choose _Origin access control settings (recommended)_. Then, _Create New OAC_. This step is to disallow direct access to the S3 origin.  
>+ Only allowing viewers to access the content via CloudFront. Hence, disallowing direct access to the origin - in this case, it will be S3.  

![image](personalAssets/Images/Projects/ResumeChallenge/5_HTTPS_2_OAC.png)
  
2. Under Default cache behavior > Viewer protocol policy, select _Redirect HTTP to HTTPS_.  
>+ Force viewers to use HTTPS only.  
![image](personalAssets/Images/Projects/ResumeChallenge/5_HTTPS_3_Redirect HTTP to HTTPS.png)
   
3. Under Custom SSL certificate - optional, select your custom SSL certificate.  
![image](personalAssets/Images/Projects/ResumeChallenge/6_DNS_6_Update SSL Cert.png)
  
4. Under **Alternate domain name (CNAME) - optional**, click _Add Item_ and type in your subdomain to be redirected to your distribution.  
5. Under **Default root object - optional** enter _index.html_. So that when viewers access the root URL, it will automatically view index.html.
>**Learning Point**  
I missed this step, causing some issues when accessing the URL later.
  
![image](personalAssets/Images/Projects/ResumeChallenge/6_DNS_7_Update Alternative Domain Name & Default Root Object.png)
  
9. Go to Route 53 > Hosted Zones > Click on your domain > Click _Create Record_  
![image](personalAssets/Images/Projects/ResumeChallenge/6_DNS_9_Create Hosted Zone Record.png)
  
10. Create 2 records via simple routing to your cloudfront distribution.  
![image](personalAssets/Images/Projects/ResumeChallenge/6_DNS_10_Create 2 Simple Record.png)
  
11. Test your website. In my case, I should enter:  
    - http://www.ckq123.click
    - http://ckq123.click  
    Both should return me to the static page (resume) with the correct SSL certificate.  
![image](personalAssets/Images/Projects/ResumeChallenge/6_DNS_11_Test Website Access via own domain.png)
  
>**Learning Point**  
I was having an issue where both _https://ckq123.click_ & _http://ckq123.click_ was providing me with an 403 error.  
Issue was fixed as I realised I needed to add ckq132.click in CloudFront **Alternate domain name (CNAME) - optional** too.  

![image](personalAssets/Images/Projects/ResumeChallenge/6_DNS_Learning Point_403 Error.png)
  
![image](personalAssets/Images/Projects/ResumeChallenge/6_DNS_Learning Point_Add Alternative Domain Name.png)
  
  
Continue to [Part 2](https://c-kq.github.io/posts/CloudResumeChallenge-Part2/)...


<!--
4. Leave the rest as default values, scroll down and click _Create Distribution_.  
5. Copy the S3 bucket policy, and return to the S3 bucket where your website is hosted.  
![image](personalAssets/Images/Projects/ResumeChallenge/5_HTTPS_6_Copy Bucket Policy.png)
  
6. Go to Permissions > Bucket Policy and click _Edit_  
![image](personalAssets/Images/Projects/ResumeChallenge/5_HTTPS_7_Edit Bucket Policy.png)
  
7. Replace the current policy with the copied policy. Then click _Save Changes_.  
![image](personalAssets/Images/Projects/ResumeChallenge/5_HTTPS_8_Replace Policy.png)
  
8. Test if you are able access directly to S3 Origin. It should fail.  
![image](personalAssets/Images/Projects/ResumeChallenge/5_HTTPS_9_Check S3 Origin Direct Access.png)
  
9. For security hygiene purposes, go back to the S3 bucket, and revert back step 2 & 3 under [Static Website](#4-static-website) as we do not need the public access anymore.  
![image](personalAssets/Images/Projects/ResumeChallenge/5_HTTPS_10_Revert Block Public Access.png)
  
![image](personalAssets/Images/Projects/ResumeChallenge/5_HTTPS_10_Revert Static Web Hosting.png)
  
10. Lastly, access the static webpage by going to the CloudFront Distribution > General > Under Details, copy the Distribution domain name.
![image](personalAssets/Images/Projects/ResumeChallenge/5_HTTPS_11_CloudFront Distribution Link.png)
  
12. Enter the URL you copied, change to _http_ & add '_/index.html_' to the back.
    In this instance, it is: http://dgiybnjyd4or4.cloudfront.net/index.html.  
    Check that it should redirect you back to HTTPS. Check the cert if it is valid.  
![image](personalAssets/Images/Projects/ResumeChallenge/5_HTTPS_12_Static Page via CloudFront.png)
-->
