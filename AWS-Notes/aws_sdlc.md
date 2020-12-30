# Software Development Life Cycle
## What is CI/CD?
Continuous integration/Continuous deployment. Allows you to build faster, decrease code review time, is completely automatic, and finds faults quicker.

## CodeCommit
Basically Github but for AWS.

## CodeBuild
A fully managed build service for AWS. It compiles code, runs unit tests, produces artifacts that are ready to deploy, and eliminates the need to provision/manage your own build servers. It provides prepackaged build environments
and allows you to build your own custom build environments. It also scales automatically. You can also automate builds based on scheduling.

## CodeDeploy
A managed deployment service that automates deployments to EC2 instances, on-premises instances, and Lambda functions. Easy to deploy new features, update lambda functions, avoid downtime during deployment, and handle the deployment
process without human intervention.

In IAM, create a new EC2 role that has CodeDeploy full access and S3 full access. Create a CodeDeploy role as well and AWS should automatically select the `AWSCodeDeployRole`. Then EC2, create a new Application Load Balancer that allows HTTP 
at port 80. When creating the EC2 instance(s), select `Launch into Autoscaling group` and use the EC2 CodeDeploy role we just created. Under `Advanced Details`, run the following bootstrap script:
```
#! /bin/bash
sudo apt-get update -y
sudo apt-get install ruby -y
sudo apt-get install wget -y
cd /home/ubuntu
#the region in the url might be dependant on what region you're using for the EC2 instance
wget https://aws-codedeploy-us-east-1.s3.amazonaws.com/latest/install 
chmod +x ./install
sudo ./install auto 
service codedeploy-agent start
rm install
```