# Set up a CI/CD pipeline with Jenkins to host an AWS S3 static website

## Introduction

In this project, I'll be creating a CI/CD pipeline using Jenkins to deploy a static website. Specifically, I'll utilize Terraform to provision an AWS S3 bucket and configure it to host the static website.

## Prerequisites
Before getting started, ensure that the following prerequisites are in place:
1. A text editor (e.g. Visual Studio Code).
2. Jenkins installed. (Instructions for deploying Jenkins on an AWS EC2 instance can be found at: [Link](https://github.com/Glen-Poh/Jenkins-Server-EC2) )

## Configuration Files

### Terraform Files

1. **provider.tf**: This file contains the provider definition (AWS) and the region(us-east-1) for the architecture setup.

2. **s3bucket.tf**: This file contains the configuration code for setting up the S3.
   
3. **output.tf**: This file contains the output for the S3 bucket website endpoint.

### HTML

1. **index.html**: A simple HTML template file to demonstrate the functionality of the static website.

2. **error.html**: This page is displayed in case of any errors encountered with the static website.

### Jenkinsfile

A text file that define the Jenkins pipeline to deploy the S3 bucket.

#### Pipeline Stages
1. Checkout from SCM: Checkout the Jenkinsfile and Terraform script for the git repo

2. Terraform init: To initialize the Terraform. This stage is skipped if the user selects 'terraform destroy'.

3. Terraform plan: Create a plan for apply or destroy.

4. Terraform apply/destroy: User will need to review the plan at the previous stage and provide approval before this stage is triggered. This stage would be either apply or destroy base on the user selection. 

## Getting Started

### Cloning from the repository

Open Git Bash and change the current working directory to the location where you want the cloned directory to be.
```
cd '/c/Users/YOUR USERNAME HERE/YOUR FOLDER HERE'
git clone https://LINKFROMGITHUB
```

### Jenkins setup
#### Setting up credentials

To provide Jenkins with the access to AWS, ensure that you have created an IAM user with an access ID and key. I have given AdministratorAccess to this IAM user. Click Manage Jenkins > Credentials.

<img src="images/1_jenkin_manage.png" alt="image" style="width:600px;"/>

In the 'New Credential' section, change the 'Kind' to 'Secret Text' and enter the Access Key ID and Access Key Secret.

<img src="images/2_jenkin_accesskey.png" alt="image" style="width:600px;"/>
<img src="images/3_jenkin_secret.png" alt="image" style="width:600px;"/>

You should have 2 credential entered under the global credential.

<img src="images/4_jenkin_credential.png" alt="image" style="width:600px;"/>

#### Creating Pipeline

On the Jenkins homepage, click on 'New Item' or 'Create a Job'. Enter your project name, select 'Pipeline', and then click 'OK'

<img src="images/5_jenkin_create_project.png" alt="image" style="width:600px;"/>

Enable 'This project is parameterized', then add a parameter named 'action', and provide 'apply' and 'destroy' as choices for this parameter.

<img src="images/6_jenkin_configure.png" alt="image" style="width:600px;"/>

If you want your pipeline to automatically trigger when there's an update to the Git repo, ensure to select 'Poll SCM'. However, for this project, I did not choose this option.

<img src="images/7_jenkin_configure.png" alt="image" style="width:600px;"/>

In the Pipeline Definition section, choose 'Pipeline script from SCM', then enter the repository URL.

######################## Placeholder ###################################

In the 'Branch Specifier' field, change it to */main, and in the 'Script Path' field, enter Jenkinsfile.

<img src="images/9_jenkin_configure.png" alt="image" style="width:600px;"/>

After saving the pipeline, navigate to the sidebar and click 'Build with Parameters'. Then, under 'action', select 'apply'.

<img src="images/10_jenkin_pipeline.png" alt="image" style="width:600px;"/>

The pipeline will run and pause at the Terraform apply/destroy stage. Approval is necessary before proceeding to the Terraform apply stage. Users must review the logs generated during the Terraform plan stage and provide their approval. See run #2 from the image below.

<img src="images/11_jenkin_pipeline.jpg" alt="image" style="width:600px;"/>

Once reviewed, click 'Approve', and the pipeline should resume. You should observe the pipeline's successful execution.

<img src="images/12_jenkin_pipeline.png" alt="image" style="width:600px;"/>

## Results

After successful execution of the pipeline, you will be able to view the created S3 bucket, along with both the index.html and error.html files contained within.

<img src="images/13_aws_s3_2.png" alt="image" style="width:600px;"/>

Visit the S3 website endpoint and you should be able to view the html page.

<img src="images/14_website1.png" alt="image" style="width:600px;"/>

The S3 static website is succefully deployed via a Jenkins pipeline.

## Testing

I've updated the HTML file with "Hello again!" and pushed it to the GitHub repository.

<img src="images/15_update_indexhtml.png" alt="image" style="width:600px;"/>

Run the Jenkins pipeline line again to apply these update.

<img src="images/16_jenkins_indexhtml.png" alt="image" style="width:600px;"/>

The static website should be updated with the changes.

<img src="images/17_website2.png" alt="image" style="width:600px;"/>

If there is any error, the error.html should appear.

<img src="images/18_website_error.png" alt="image" style="width:600px;"/>

## Deleting the infrastructure

To remove the S3 bucket deployed via the Jenkins pipeline, click 'Build with Parameters' and select 'destroy' under the action.

<img src="images/19_jenkins_destroy.png" alt="image" style="width:600px;"/>

If the 'destroy' parameter is selected, the pipeline will skip 'terraform init' and trigger 'terraform apply -destroy' under the Terraform plan stage.

<img src="images/20_jenkins_destroy_plan.png" alt="image" style="width:600px;"/>

Review the destroy plan and click 'Approve' to allow the pipeline to continue.

<img src="images/21_jenkins_destroy_review.png" alt="image" style="width:600px;"/>

<br>
Verify that the S3 bucket has been removed using the AWS console.
<br><br>
If you are no longer using the Jenkins Server, remember to delete it too.