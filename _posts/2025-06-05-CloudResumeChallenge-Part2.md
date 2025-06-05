---
title: "Project 1: Cloud Resume Challenge Part 2"
date: 2025-06-05
categories: [Cloud Projects]
tags: [AWS]
---


See Part 1 Here...


## 6. DNS  
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
I realised I should have done step 8 in the HTTPS segment so that I did not need to append 'index.html' in the link.

![image](assets/img/projects/ResumeChallenge/6_DNS_7_Update Alternative Domain Name & Default Root Object.png)
  
9. Go to Route 53 > Hosted Zones > Click on your domain > Click _Create Record_  
![image](assets/img/projects/ResumeChallenge/6_DNS_9_Create Hosted Zone Record.png)
  
10. Create 2 records via simple routing to your cloudfront distribution.
![image](assets/img/projects/ResumeChallenge/6_DNS_10_Create 2 Simple Record.png)

11. Test your website. In my case, I should enter:  
    - http://www.ckq123.click
    - http://ckq123.click  
    Both should return me to the static page (resume) with the correct SSL certificate.  
![image](assets/img/projects/ResumeChallenge/6_DNS_11_Test Website Access via own domain.png)
  
>**Learning Point**  
I was having an issue where both _https://ckq123.click_ & _http://ckq123.click_ was providing me with an 403 error.  
Issue was fixed as I realised I needed to add ckq132.click in CloudFront **Alternate domain name (CNAME) - optional** too.  

![image](assets/img/projects/ResumeChallenge/6_DNS_Learning Point_403 Error.png)
  
![image](assets/img/projects/ResumeChallenge/6_DNS_Learning Point_Add Alternative Domain Name.png)
