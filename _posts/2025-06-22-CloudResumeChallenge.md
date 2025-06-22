---
title: "Project 1: Cloud Resume Challenge"
date: 2025-06-22
categories: [Cloud Projects]
tags: [Cloud Resume Challenge]
---

# Cloud Resume Challenge  
Like many others, I've chosen the Cloud Resume Challenge as my initial project. Below, you'll find the documentation outlining my progress and learnings. The full challenge requirements are available [here](https://cloudresumechallenge.dev/docs/the-challenge/aws/).  
  
  
## Stage 1: Building a Static Website
To create the static resume page, I primarily used HTML containers (divs) and CSS to replicate the layout of my original resume as closely as possible. I employed several CSS techniques to achieve near-perfect formatting and color matching:  
- I used **percentage units for width and <ins>margin: auto</ins>** to ensure the page is responsive and displays well across different browser sizes.
- I selected **<ins>font-family</ins> and <ins>font-weight</ins>** to closely match the typography of my original document.
- I fine-tuned spacing using **<ins>margin</ins> and <ins>padding</ins>** to eliminate any 'awkward' gaps.
- For table displays, I combined **<ins>inline-block</ins> with <ins>border-box</ins> and <ins>text-align</ins>**..
- To match the original document's colors accurately, I used **PowerPoint's color picker** and applied the resulting **HEX values** for background and line colors.
  
![image](personalAssets/Images/Projects/ResumeChallenge/3_CSS.png)
  
Next, I have used S3 to store all the HTML, CSS & JavaScript(later) files.  
For a temporary solution, I enabled static website hosting on S3 to check if all is running well. The steps for static hosting for S3 are listed below...
  
1. **Enable Static Web Hosting**: Under the S3 bucket's properties, I enabled static web hosting and specified the default index document.  
![image](personalAssets/Images/Projects/ResumeChallenge/4_Static_2_Static Web Hosting.png)
  
2. **Unblock Public Access**: In the Permissions section, I unblocked all public access to the bucket.  
![image](personalAssets/Images/Projects/ResumeChallenge/4_Static_3_Unblock Public Access.png)  
  
3. **Set Bucket Policy**: I then configured the bucket policy to grant public read access, referencing the [AWS Documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/HostingWebsiteOnS3Setup.html#step4-add-bucket-policy-make-content-public) for the appropriate policy structure (remembering to update the ARN).  
![image](personalAssets/Images/Projects/ResumeChallenge/4_Static_4_Bucket Policy.png)  
  
4. **Access Website**: Finally, I retrieved the bucket website endpoint from the properties section to access the hosted static website.  
![image](personalAssets/Images/Projects/ResumeChallenge/4_Static_5_S3 Static Web Link.png) 
  
  
## Stage 2: Securing the Website with HTTPS
_(Note: I'm using an existing domain registered on AWS for this step.)_  
  
To secure the website with HTTPS, I implemented Amazon CloudFront in conjunction with AWS Certificate Manager (ACM) for a public SSL certificate. CloudFront offers several benefits for this setup:  
+ Deliver the content closer to the viewers, enabling faster load time.
+ Force viewers to use HTTPS only.
+ It restricts direct access to the S3 origin via Origin Access Control(OAC), allowing content to be served exclusively through CloudFront for better security.
  
  
### AWS Certificate Manager (ACM) 
The first step was to create a public SSL certificate using AWS Certificate Manager (ACM). It's crucial to ensure you're in the N. Virginia (us-east-1) region when doing this.
  
1. Head over to AWS Certificate Manager (ACM) & **Request a certificate**.  
![image](personalAssets/Images/Projects/ResumeChallenge/6_DNS_1_ACM Request Cert.png)
  
2. Ensure **Request a public certificate** is selected, then click Next.  
![image](personalAssets/Images/Projects/ResumeChallenge/6_DNS_2_Public Cert.png)
  
3. Under the Fully qualified domain name, enter the domain name which you wished to be qualified. In my case, I selected _ckq123.click_ and _*.ckq123.click_ (for future use (e.g., www)). Click **Request** once you are done.   
![image](personalAssets/Images/Projects/ResumeChallenge/6_DNS_3_Public Cert Settings.png)

4. As my domain is hosted in Route 53, I will create the CNAME records directly by clicking **Create records in Route 53** for validation. Followed by **Create Records**.  
![image](personalAssets/Images/Projects/ResumeChallenge/6_DNS_4_Create Records in Route53.png)
    
![image](personalAssets/Images/Projects/ResumeChallenge/6_DNS_4_Create Records.png)  
After a few minutes, the status should change from **Pending validation** to **Issued**. Your SSL cert is created.  
![image](personalAssets/Images/Projects/ResumeChallenge/6_DNS_4_Public Cert Issued.png)
  
  
### Amazon CloudFront  
As we are using CloudFront to host the static website, head over to CloudFront to create a distribution and set the origin to the S3 bucket (**NOT WEBSITE ENDPOINT**) that we previously used. There are a few options we need to edit while creating the distribution. They are...  
1. Under Origin access, choose _Origin access control settings (recommended)_. Then, _Create New OAC_. This step is to disallow direct access to the S3 origin.  
+ It restricts direct access to the S3 origin via Origin Access Control(OAC), allowing content to be served exclusively through CloudFront for better security.  

![image](personalAssets/Images/Projects/ResumeChallenge/5_HTTPS_2_OAC.png)
  
2. Under Default cache behavior > Viewer protocol policy, select **Redirect HTTP to HTTPS**.  
>+ Force viewers to use HTTPS only.  
![image](personalAssets/Images/Projects/ResumeChallenge/5_HTTPS_3_Redirect HTTP to HTTPS.png)
   
3. Under Custom SSL certificate - optional, select your custom SSL certificate.  
![image](personalAssets/Images/Projects/ResumeChallenge/6_DNS_6_Update SSL Cert.png)
  
4. Under **Alternate domain name (CNAME) - optional**, click **Add Item** and type in your subdomain to be redirected to your distribution.  
5. Under **Default root object - optional** enter _index.html_. So that when viewers access the root URL, it will automatically view index.html.
>**Learning Point**  
I missed this step, causing some issues when accessing the URL later.
  
![image](personalAssets/Images/Projects/ResumeChallenge/6_DNS_7_Update Alternative Domain Name & Default Root Object.png)
  
9. Go to Route 53 > Hosted Zones > Click on your domain > Click **Create Record**  
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
  
  
## Stage 3: Dynamic Website - Page Views Counter
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
  
  
## Stage 4: Infrastructure as Code (IAC) - Terraform
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
  
  
## Stage 5: Continuous Integration & Deployment (CI/CD)  
This final stage proved more extensive than initially anticipated, and even after completing the project, I feel I've only scratched the surface of CI/CD 😅. The task involved setting up both backend and frontend repositories and automating resource deployments using Infrastructure as Code (IaC) within GitHub Actions. To solidify my understanding of GitHub Actions, I reviewed various resources and tutorials. One particularly helpful resource was [CoderDave](https://www.youtube.com/@CoderDave)'s [GitHub Actions Tutorial](https://www.youtube.com/watch?v=TLB5MY9BBa4).  
  
For a start, I began by deploying a simple S3 bucket and uploading a few files using Terraform directly from GitHub. This straightforward exercise helped me grasp how Terraform and AWS integrate with GitHub Actions, and I learned three key things:  
  
**1. Access Keys and Tokens**
For Terraform and AWS to function within GitHub Actions, it's essential to set up the necessary credentials. This involves obtaining an API token from [HCP Terraform](https://app.terraform.io/) and Access Keys from [AWS - IAM](https://us-east-1.console.aws.amazon.com/iam/) (these are the same access keys previously used for remote AWS CLI operations).  
![image](personalAssets/Images/Projects/ResumeChallenge/14_1_CICD_Token&AccessKeys.png)  
  
**2. Respository Secrets**  
To configure access for Terraform and AWS in GitHub Actions, I utilized the **hashicorp/setup-terraform** and **aws-actions/configure-aws-credentials** actions, respectively. Given the sensitive nature of these credentials, which grant access to cloud accounts, they must be securely stored. I learned the importance of storing them as GitHub Secrets. This encrypts the credentials, making them accessible only through the workflow files and preventing exposure in public repositories or logs.  
![image](personalAssets/Images/Projects/ResumeChallenge/14_2_CICD_Token&AccessKeys_ActionsFile.png)  
  
**3. Ephemeral Storage**  
Each GitHub Actions run is provisioned on demand, meaning any data stored within them is ephemeral. Unlike previous local Terraform runs where state files were stored directly on my machine, for CI/CD, the Terraform state needs to be stored persistently in the cloud. To achieve this, I provisioned an **S3 bucket** for storing the state files and a **DynamoDB table** for state locking, which prevents concurrent modifications and ensures consistency.  
  
With this understanding, I proceeded to deploy the remaining Terraform files, including those for the static website resources, into my Backend Repository. A crucial change was adding a **backend configuration** to my Terraform files to reflect the new persistent storage location for state files.  
![image](personalAssets/Images/Projects/ResumeChallenge/14_4_CICD_S3Backend.png)  
  
Additionally, I integrated environment variables into the GitHub Actions YAML file allowing me to reference these variables directly within my Terraform variable configuration files.  
![image](personalAssets/Images/Projects/ResumeChallenge/14_3_CICD_EnvironmentVariable.png)  
  
Once all initial configurations were verified, I segregated the website's static resources into a dedicated **Frontend Repository**. To enable the backend workflow to access these files, I configured the workflow in the **Backend Repository** to pull resources from the **Frontend Repository**. This was achieved by supplying my **GitHub Account's Personal Access Token (PAT)** via secrets to the **actions/checkout@v4 action**, granting the necessary permissions.  
![image](personalAssets/Images/Projects/ResumeChallenge/14_5_CICD_GHPAT.png)  
  
The next requirement was to automate updates: any changes made to the static website in the Frontend Repository needed to automatically trigger a deployment to the live site. I accomplished this by leveraging repository_dispatch and using a keyword (in my case it was '_update-website-files_') to call the backend to run the workflow again.  
![image](personalAssets/Images/Projects/ResumeChallenge/14_6_CICD_RespositoryDispatch.png)  
  
>**Learning Point**  
Although the repository_dispatch trigger was functional, the static website files weren't actually updating in S3. From Terraform's perspective, no changes were detected because the filenames remained the same (e.g., index.html).  
  
To resolve this, I introduced the source_hash attribute to the file upload process in Terraform. This attribute ensures that **every update to a file results in a different hash value**. By providing this unique hash, Terraform now correctly identifies changes in the file's content, even if the filename is identical, and thus triggers the necessary update in S3.  
  
![image](personalAssets/Images/Projects/ResumeChallenge/14_7_CICD_S3SourceHash.png)  
  
Finally, I implemented automated testing using **pytest** combined with **moto**, which simulates a fake AWS environment. This allowed me to verify three critical aspects of the Lambda function. The function is using the correct...  
1. DynamoDB table name
2. Table's attributes and values names
3. Attribute value type (number)
  
![image](personalAssets/Images/Projects/ResumeChallenge/14_8_CICD_Pytest.png)  
  
After developing the test file, I deployed it to GitHub. I then configured a GitHub Action to install the necessary dependencies and execute these tests. Once this test workflow successfully passes, it triggers the "Terraform Apply" workflow using a **workflow_call** coupled with the **needs** attribute. This ensures that the infrastructure deployment (Terraform Apply) only proceeds after the tests have passed.  
![image](personalAssets/Images/Projects/ResumeChallenge/14_9_CICD_PytestGithubActions.png)  
  
![image](personalAssets/Images/Projects/ResumeChallenge/14_10_CICD_WorkflowPass.png)  
  
![image](personalAssets/Images/Projects/ResumeChallenge/14_11_CICD_WorkflowFail.png)  
  
>**Learning Point 1**  
While looking through resources, I learnt that testing involves multiple sessions across different versions and OS. Hence, a **matrix** is required to run the test. One thing I missed out while testing was the runtime cost in GitHub. With the use of **matrix** in GitHub Actions, each run is multiplied by the number of versions, OS and more if there are other variables. I had initially included macOS in my testing matrix. Crucially, as [macOS runners have a 10x minute multiplier](https://docs.github.com/en/billing/managing-billing-for-your-products/about-billing-for-github-actions#minute-multipliers), this choice caused my free tier for the month to be immediately exceeded after just a few runs! This was a costly, but valuable, lesson in optimizing CI/CD workflows for cost efficiency.  
  
>**Learning Point 2**  
Another important discovery regarding GitHub Actions was that while calling a workflow from within the same repository is straightforward using **workflow_call**, credentials are not **automatically inherited** by the called workflow. Failing to pass these secrets will result in an error. To ensure the called workflow can access necessary sensitive information, the caller workflow must explicitly use the **secrets: inherit** attribute.  

![image](personalAssets/Images/Projects/ResumeChallenge/14_12_CICD_WorkflowCall.png)  
  
  
## Conclusion  
Completing the Cloud Resume Challenge proved to be an invaluable capstone. Solidifying my journey from theoretical understanding to practical application in cloud engineering. This project not only allowed me to meticulously build a secure static website by leveraging core AWS services like **S3, CloudFront, Route 53, ACM, DynamoDB, Lambda, and API Gateway**, but it also served as a playground for applying and expanding upon the technical knowledge acquired through my AWS - SAA certification.  
Crucially, the hands-on implementation of **Infrastructure as Code (IaC)** with **Terraform** and the development of a **CI/CD pipeline** using **GitHub Actions** were transformative. These aspects pushed me beyond theoretical concepts as by doing lots of research and countless trial-and-error, it enabled me to be exposed to essential new skills in infrastructure automation & secure deployment practices for cloud resources, practical troubleshooting and critical lessons in cost optimization and dependency management. The Cloud Resume Challenge has, therefore, not just been a project completion, but a comprehensive training ground that has equipped me with real-world ready skill set for a career in cloud engineering.

