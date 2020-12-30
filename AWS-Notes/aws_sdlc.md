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


Then head to `CodeDeploy` and then create your application. From there you can create the `Deployment group` and the service role for that group should be our CodeDeploy role we created earlier. From there, we can select our autoscaling group in env config and then follow along the steps to complete the creation of the deployment group. When our deployment group is created, we can then create our deployment. Using the CodeDeploy `create deployment` option forces us to choose between S3 as our repo or Github as our repo, not CodeCommit. We can use CodePipline for that. Once the S3/Github code is linked to our deployment, we can create our deployment and it'll take a few minutes deploying onto our EC2 group we created. If we want a custom DNS URL to link to our site instead of the DNS end point for our loadbalancer, we can use Route53 to buy a URL and then create a record set with that URL and link it to our loadbalancer. Our repo should contain an `appspec.yml` file pointing to our source files in the repo and the destination on our instances and any scripts we need to run to install dependencies, run at server startup, and run at server shutdown. For example,
```
version: 0.0
os: linux
files:
  - source: /index.html
    destination: /var/www/html
  - source: /my_image.png
    destination: /var/www/html
  - source: /home_icon.png
    destination: /var/www/html
hooks:
  BeforeInstall:
    - location: scripts/install_dependencies
      timeout: 300
      runas: root
    - location: scripts/start_server
      timeout: 300
      runas: root
  ApplicationStop:
    - location: scripts/stop_server
      timeout: 300
      runas: root
    
```

## CodePipeline
CodePipeline is the Continuous Deployment part of CI/CD. It's automatic from check-in of the code to deployment onto servers. It comes with prebuilt plugins you can use to configure it or make your own. It gives you a lot of control over configuring all the stages of the software release process.

When creating a new pipeline, we can now use CodeCommit as the source destination of our code. We select our CodeBuild config (optional) and then select our Deployment group and then CodePipeline handles the rest.


## Testing
To add testing to our pipeline, we can edit the pipeline to add a stage and then select one of the testing options under `Action Provider`.

## Deployment Strategies
### Single Target 
Go straight from build to deploying on target server.
### All-at-once Deployment
 Same as single target, except we have multiple target servers instead of just the one. Like single target, there's no testing and less than ideal rollback. Both single target and all-at-once are rarely used on large applications.
### Minimum in-service deployment
Deployments happen in stages where we specify how many target servers should remain in service with the old build while we deploy to the other servers. If you have 5 servers, you could set 2 minimum in-service servers who won't get the new deployment initially so they can stay online while the others are updated. Once the other servers are done updating, the older servers are updated in the next stage. Because of the complexity and need for orchestration tools, it's done on larger applications. Also allows for testing. Generally no downtime.
### Rolling Deployment
 We need significant orchestration. The update is rolled out in stages where we specify how many servers are updated per stage (think inverse of minimum service). If we have 10 servers and set it up so 2 servers are updated per stage, we'd have 5 stages where our deployment is rolled out over the servers. It allows for automated testing. Depending on how we set it up, it can be the least efficient deployment in terms of time taken. Our deployments can also be paused, allowing limited multi-version testing.
### Blue Green Deployment
 We deploy to an entirely new environment to prevent outage risks. We can have our old application running on servers 1, 2, and 3, or our blue servers, while we setup, test, and verify our new deployment on servers 4, 5, and 6. When we determine the new, or green, servers are ready for production use, we move our application domain over to point to the new servers. Gives us a lot of stability and easy to roll back by simply point our domain name to old servers but costs more because we use twice the regular amount of servers initially. The entire process can be automated using tools like CloudFormation.
 ### Canary Deployment
 Similar to blue green deployment, we have a cluster of servers running the old deployment and we create and setup a new cluster running the new deployment. Instead of switching over the domain pointer and instantly moving over to the green servers, we slowly route the traffic from blue to green. Route53 calls this weighted round robin. We can eventually move 100% of users over to our green servers. 