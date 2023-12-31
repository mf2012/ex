# DevOps Problem:
Setup

Docker > Jenkins > AWS Cloudformation

Task: Your task is to describe, diagram and document a CI/CD pipeline to deploy a Node.js application onto an AWS Elastic Beanstalk instance.

You should:
1. [x] Write a Dockerfile to containerize the Node.js application.

2. [x] Configure a Jenkins job to build the Docker image and push it to an AWS ECR repository.

3. [x] Write CloudFormation templates to create the required infrastructure (e.g. Elastic Beanstalk
instance, RDS instance, etc.).

4. [x] Configure Jenkins to deploy the Docker image to the Elastic Beanstalk instance using the
CloudFormation templates.

DISCLAIMER: My app is the simple hello world! nodejs app


# Requirements
- jenkins user must have AWS account with the admin access to the ECR and other required services set up. 
It can be provided via jenkins security credentials(recommended) or configured via ssh user on the server(not recommended)
Additionally, jenkins user should be able to use worker node where it can install software without password. ex. sudo *command*
- I've added also Jenkins Blue Ocean plugin for better representation of the pipeline
- create Access key with access secret and configure your aws cli on the box.

# Diagrams
General process is captured in the Jenkinsfile but below is the graphical representation to make it easier to understand all steps.
![ci_cd_view_blue_ocean](./picts/ci_cd_jenkins.png)

Additional diagram to show general dependency
![Jenkins_dev_AWS_dependency](./picts/CICD_process.drawio.png)

# My comments
- Personally, I prefer using Terraform for IaC but use of cloudfront templates is very convenient :D
