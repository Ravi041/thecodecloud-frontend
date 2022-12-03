pipeline {
   agent any
   environment {
        scannerHome = tool "SonarScanner"
        AWS_ACCOUNT_ID="981864830773"
        AWS_PROFILE="jenkins.aws"
        AWS_DEFAULT_REGION="us-east-1"
        IMAGE_REPO_NAME="thecodecloud-frontend"
        IMAGE_TAG="${env.BUILD_NUMBER}"
        REPOSITORY_URI = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}"
   }

    stages {
        stage('Git Checkout') {
            steps {
            script{
                git branch: 'main', credentialsId: 'GitHub', url: 'https://github.com/Ravi041/thecodecloud-frontend.git'  
                }  
            }
        }
        stage('Static code Analisys'){
            steps {
            script{
               def scannerHome = tool 'SonarScanner';
               withSonarQubeEnv() {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }    
            
    }
        stage('Logging into AWS ECR') {
            steps {
            script {
               // sh """aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"""
              sh "docker login -u AWS -p $(aws ecr get-login-password --region us-east-1) 981864830773.dkr.ecr.us-east-1.amazonaws.com"
            }
        }
    }
	// Building Docker images
        stage('Building image') {
            steps{
            script {
                dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
            }
        }
    }
    	stage('Pushing to ECR') {
		    steps{
		    script {
                sh "docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"
                sh "docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"
			}
		  }
		}
        stage('Trigger Update K8s') {
            steps{
            script {
                echo "triggering Update manifest Job"
                build job: 'frontendapp-updatek8s', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
            }
        }
    }  
   }
}