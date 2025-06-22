---
title: "Project 1: Cloud Resume Challenge Part 3"
date: 2025-06-21
categories: [Cloud Projects]
tags: [Cloud Resume Challenge - AWS]
---
Go back to [Part 2](https://c-kq.github.io/posts/CloudResumeChallenge-Part2/)...  
  
  
## Continuous Integration & Deployment (CI/CD)  
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
Crucially, the hands-on implementation of **Infrastructure as Code (IaC)** with **Terraform** and the development of a **CI/CD pipeline** using **GitHub Actions** were transformative. These aspects pushed me beyond theoretical concepts, enabling me to be exposed to essential new skills in infrastructure automation & secure deployment practices for cloud resources, and practical troubleshooting, including critical lessons in cost optimization and dependency management. The Cloud Resume Challenge has, therefore, not just been a project completion, but a comprehensive training ground that has equipped me with a confident, real-world ready skill set for a career in cloud engineering.



