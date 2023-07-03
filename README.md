# AWS CICD Pipeline Code Deployment to AWS EC2 Instance
### User Data for Dependencies installations for AMAZON Linux

**Pre-Requsites**
1. IAM Roles
	> Trusted Entity: AWS Service, Use case: EC2, Permissions policies: AmazonEC2RoleforAWSCodeDeploy, Role name: EC2CodeDeploy
	> Trusted Entity: AWS Service, Use case for AWS service: CodeDeploy; Select CodeDeploy, Permissions policies: AWSCodeDeployRole, Role name: CodeDeployRole
2. Create EC2 Instance 
	> Amazon Linux
	> With KeyPair
	> Under Advances details >> IAM instance profile: Choose EC2CodeDeployn role
	> User data: 
				```
				#!/bin/bash
				sudo yum -y update
				sudo yum -y install ruby
				sudo yum -y install wget
				cd /home/ec2-user
				wget https://aws-codedeploy-ap-south-1.s3.ap-south-1.amazonaws.com/latest/install
				sudo chmod +x ./install
				sudo ./install auto
				sudo yum install -y python-pip
				sudo pip install awscli
				```
3. Create Code Deploy (Search for CodePipeline)
	> Expand Deploy . CodeDeploy: Click on **Create application**, Application name: Seetha-CICD, Compute platform: EC2/On-premises
	> Click on **Create deployment group**, Deployment group name: Seetha-CICD-DP, Service role: CodeDeployRole, Environment configuration: Tick on "Amazon EC2 instance", Deployment Settings: CodeDeployDefault.HalfAtATime; TAG::Key:Name, Value: Seetha-CICD

4. Create Pipeline
	> Pipeline name: Seetha-CICD-PIPELINE,NEXT,Source provider: GitHub (version 2), Click on "Connect to Github", Connection name: Seetha-CICD-GIT, Click on "Install a new app"; Repository name: Choose "aws_cicd_pipline_deploy", branch name: main, NEXT, "Skip build stage", Deploy provider: AWS CodeDeploy, Application name:Seetha-CICD, Deployment group name: Seetha-CICD-DP

5. Enable ports for EC2 Instance to access it from outside
	> Select the EC2 instance, Select Security tab, Click on Security Group, "Edit inbound rules"
		"Add rule", Type: HTTP, Source: Anywhere-IPv4, "Save rule"
		"Add rule", Type: SSH, Source: Anywhere-IPv4, "Save rule"
 
