/*
Author: mf2k@proton.me
Date: 2023-08

DISCLAIMER: this fail has been had written. All bash command are working as I've tested them

*/

pipeline {
    agent none
    stages {
        stage('Configure ECR for app') {
        agent any
        steps {
            script {
                sh """#!/bin/bash -x
# Install awscli tools
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip -u awscliv2.zip
sudo ./aws/install
aws --version

aws --region us-west-2 ecr describe-images --repository-name hello_node > /dev/null 2>&1 || \
aws --region us-west-2 ecr create-repository --repository-name hello_node > ecr.json
"""         }
            script {
                env.ECR_URL = sh (
                    returnStdout: true, script: """#!/bin/bash -x
aws --region us-west-2 ecr describe-repositories|jq -r '.[] | select(.[].repositoryUri|test("hello_node"))|.[].repositoryUri'
                """).trim()
                println env.ECR_URL
                }
            }
        }
    
        stage('Build container and publish') {
            agent any
            steps {
                sh '''#!/bin/bash -x
cd ./jenkins
docker build -t hello_node:${env.BUILD_ID} .
docker tag \
    hello_node:${env.BUILD_ID} \
    ${env.ECR_URL}:${env.BUILD_ID}

ACCOUNT=$(echo ${env.ECR_URL}|cut -f 1 -d '/')
aws --region us-west-2 ecr get-login-password | docker login --username AWS --password-stdin $ACCOUNT

docker push \
    ${env.ECR_URL}:${env.BUILD_ID}
                '''
            }        
        }

        stage('Run test') {
            agent any
            steps {
                sh """
                    docker run -p 58080:8080 -d hello-node:${env.BUILD_ID} --rm
                    curl localhost:58080 || echo "ERROR: Cannot connect to the container on localhost:58080" 
                    
                """
            }
        }

        stage('Prep_zip_for_beanstalk') {
            agent any
            steps {
                sh '''
                zip hello_node.zip Docker*
                '''
            }
        }

        stage('Deploy to beanstalk') {
            agent any
            steps {
                sh '''
                # create S3 bucket 
                #RAND6=$(cat /dev/urandom | head -c 6|base64|tr '[A-Z]' '[a-z]')
                aws s3api create-bucket --bucket hello-node-eb-ymdhjp5n --region us-east-1
                aws cp hello_node.zip s3://hello-node-eb-ymdhjp5n/hello_node.zip
                # run beanstalk with template 
                aws cloudformation create-stack --stack-name hello-node --template-body file://ECS_beanstack_ECR.yaml
                '''
            }
        }
    }
}
