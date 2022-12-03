# thecodecloud-frontendapp

Install nodejs, git, docker in the jenkins server & add git in global tool configuration.
install aws cli in the jenkins server and inject aws creds using "aws configure".


add jenkins into docker group, provide sudo access to jenkins via visudo & change (sudo chmod 666 /var/run/docker.sock)
create .aws folder under jenkins home dir --> /var/lib/jenkins and copy aws credentials inside newly created .aws folder
Jenkins Plugins - ECR, Docker pipeline, AWS Credentials, Sonarqube integration.
