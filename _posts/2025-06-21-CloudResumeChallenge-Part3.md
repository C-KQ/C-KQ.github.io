---
title: "Project 1: Cloud Resume Challenge Part 3"
date: 2025-06-21
categories: [Cloud Projects]
tags: [Cloud Resume Challenge - AWS]
---
Go back to [Part 2](https://c-kq.github.io/posts/CloudResumeChallenge-Part2/)...  
  
## Continuous Integration & Deployment (CI/CD)  
Final sprint! Whew... This requires a lot more research than I expected and I only scratch the surface of it even after completing this project 😅. For CI/CD, the task was to create a backend and frontend respository and automate the deployment of resources after testing using IAAC in GitHub Actions. To brush up my understanding of how GitHub Actions work, I read some resources and worked on some tutorials. One of the better resources I learnt from was by [CoderDave](https://www.youtube.com/@CoderDave)'s [GitHub Actions Tutorial](https://www.youtube.com/watch?v=TLB5MY9BBa4).  
  
For a start, I deployed a simple S3 bucket and uploaded a few files into it using Terraform, just to get a hang of how Terraform & AWS work in GitHub. This simple process allowed me to apply & learn three things:  
  
<ins>1. Access Keys and Tokens</ins>  
For Terraform and AWS to work in Github, you would need to get API token from [HCP Terraform](https://app.terraform.io/) & Access Keys (_same access keys used to run AWS CLI remotely previously_) from [AWS - IAM](https://us-east-1.console.aws.amazon.com/iam/).  
![image](personalAssets/Images/Projects/ResumeChallenge/14_1_CICD_Token&AccessKeys.png)  
  
<ins>2. Respository Secrets</ins>  
Followed up by using **hashicorp/setup-terraform** & **aws-actions/configure-aws-credentials** respectively to configure access to Terraform and AWS in GitHub Actions. As these credentials are sensitive and they provide access to your accounts, they must be stored in **GitHub Secrets** so that they can be encrypted and only accessible through the workflow. 
![image](personalAssets/Images/Projects/ResumeChallenge/14_2_CICD_Token&AccessKeys_ActionsFile.png)  
  
<ins>3. Ephemeral Storage</ins>
As each GitHub Actions run are being provisioned on demand, the items that we store in them are epheramal. Unlike previous Terraform runs where we store the states locally, we now have to store the states on a persistent cloud storage. As such, I spin up a S3 bucket to store the state files and DynamoDB table for state locking.  
With this knowledge in mind, I deployed the rest of the Terraform files into my Backend Respository (including the static website resources). Some changes I made include adding a backend in my Terraform configurations as I have changed the storage location of state files.  
![image](personalAssets/Images/Projects/ResumeChallenge/14_4_CICD_S3Backend.png)  
  
Also, adding environment variables into the GitHub Action YAML file for referencing in Terraform variable config files  
![image](personalAssets/Images/Projects/ResumeChallenge/14_3_CICD_EnvironmentVariable.png)  
  
After ensuring all is working, I transferred the website resources to a _Frontend Respository_ and pulled the resources from the _Backend Respository_ workflow by supplying my GitHub's Account **Personal Access Token (PAT)** via **actions/checkout@v4**.  
![image](personalAssets/Images/Projects/ResumeChallenge/14_5_CICD_GHPAT.png)  
  
Next up, the task requires any updates to the static website in the _Frontend Respository_ to publish the update to the website. This is done by using **respository_dispatch** and using a keyword (in my case it was '_update-website-files_') to call the backend to run the workflow again.  
![image](personalAssets/Images/Projects/ResumeChallenge/14_6_CICD_RespositoryDispatch.png)  
  
>**Learning Point**  
Although this works, the files was not updated due to it being the same name in S3 (e.g., index.html). In Terraform view, there were no changes made. Hence, I added another attribute before uploading the files for Terraform to compare - source_hash. This in turn, resolved the issue as every update will result in a different hash.  

![image](personalAssets/Images/Projects/ResumeChallenge/14_7_CICD_S3SourceHash.png)  
  
Lastly, I created a simple pytest to test if...
1. DynamoDB table has the correct name
2. The table's attributes and values names are correct
3. Attribute value is a number type
  
This is done by combining pytest with moto (for generating a fake AWS environment).  
![image](personalAssets/Images/Projects/ResumeChallenge/14_8_CICD_Pytest.png)  
  
Next, I deployed the test file into GitHub and run a GitHub Action to install the dependencies and run the test. Once it was successful, I deployed it to run on a workflow_call, allowing my Terraform Apply to only run after this test passed.  
![image](personalAssets/Images/Projects/ResumeChallenge/14_9_CICD_PytestGithubActions.png)  
  
![image](personalAssets/Images/Projects/ResumeChallenge/14_10_CICD_WorkflowPass.png)
  
![image](personalAssets/Images/Projects/ResumeChallenge/14_11_CICD_WorkflowFail.png)  
  
>**Learning Point 1**  
While looking through resources, I learnt that testing involves multiple tries across different versions and OS. Hence, a _matrix_ is required to run the test. One thing I missed out while testing was the runtime cost in GitHub. With the use of matrix, each run is multiplied by the number of versions, OS and more if there are other variables. I initially added macOS to the testing. As macOS has a minute multiplier of 10x, this immediately exceed my free tier for the month ☠☠. 








