# AWS CICD Pipeline Code Deployment to AWS EC2 Instance
### User Data for Dependencies installations for AMAZON Linux

**Pre-Requsites**
1. Create IAM role (Attach one with EC2 instance and another role attachk with CodeDeploy)
	> Trusted Entity: AWS Service, Use case: EC2, Permissions policies: AmazonEC2RoleforAWSCodeDeploy, Role name: EC2CodeDeploy
	> Trusted Entity: AWS Service, Use case for AWS service: CodeDeploy; Select CodeDeploy, Permissions policies: AWSCodeDeployRole, Role name: aws-codedeploy-role
	
2. Create EC2 Instance 
	> Amazon Linux
	> With KeyPair
	> Under Advances details >> IAM instance profile: Choose EC2CodeDeployn role
	> Tick "Allow HTTP traffic from Internet"
	> User data: (Install CodeDeploy agent on EC2 instace)
```
#!/bin/bash
sudo yum -y update
sudo yum -y install ruby
sudo yum -y install wget
cd /home/ec2-user
#wget https://<Bucket Name>.s3.<Region>.amazonaws.com/latest/install
wget https://aws-codedeploy-ap-south-1.s3.ap-south-1.amazonaws.com/latest/install
sudo chmod +x ./install
sudo ./install auto
sudo yum install -y python-pip
sudo pip install awscli
```
3. Create Code Deploy (Search for CodePipeline)
	> Expand Deploy . CodeDeploy: Click on **Applications && Create application**, Application name: Seetha-CICD, Compute platform: EC2/On-premises

4. Create Deployment 
	> Click on **Create deployment group**, Deployment group name: Seetha-CICD-DP, Service role: aws-codedeploy-role, Environment configuration: Tick on "Amazon EC2 instance", Deployment Settings: CodeDeployDefault.HalfAtATime; TAG::Key:Name, Value: Seetha-CICD

5. Create Pipeline
	> Pipeline name: Seetha-CICD-PIPELINE,NEXT,Source provider: GitHub (version 2), Click on "Connect to Github", Connection name: Seetha-CICD-GIT, Click on "Install a new app"; Repository name: Choose "aws_cicd_pipline_deploy", branch name: main, NEXT, "Skip build stage", Deploy provider: AWS CodeDeploy, Application name:Seetha-CICD, Deployment group name: Seetha-CICD-DP

6. Enable ports for EC2 Instance to access it from outside
	> Select the EC2 instance, Select Security tab, Click on Security Group, "Edit inbound rules"
		"Add rule", Type: HTTP, Source: Anywhere-IPv4, "Save rule"
		"Add rule", Type: SSH, Source: Anywhere-IPv4, "Save rule"
 


3. Create build
	> Expand Build and click on "Create project", Project name: django-project-build, Source provider: GitHub; Repository: Choose "Connect using OAuth", Repository URL: https://github.com/ceetharamm/blogprojectdrf.git , Operating System: Ubuntu, Runtime(s): Standard, Image: aws/codebuild/standard:6.0, Click on "Create build project"
	
4. Create Code Deploy application
	> Expand Deploy . CodeDeploy: Click on **Applications && Create application**, Application name: django-CICD, Compute platform: EC2/On-premises

5. Create Deployment Group
	> Click on **Create deployment group**, Deployment group name: django-CICD-DP, Service role: aws-codedeploy-role, Environment configuration: Tick on "Amazon EC2 instance", TAG::Key:Name, Value:django-Server, Deployment Settings: CodeDeployDefault.HalfAtATime

6. Create Pipeline
	> Pipeline name: django-CICD-PIPELINE,NEXT,Source provider: GitHub (version 2), Click on "Connect to Github", Repository: Choose "ceetharamm/blogprojectdrf", branch name: master, NEXT, Choose Build provider: AWS CodeBuild, Project name: django-project-build, Deploy provider: AWS CodeDeploy, Application name:django-CICD, Deployment group name: django-CICD-DP
