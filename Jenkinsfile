pipeline {
	agent any
	environment {
		DOCKERHUB_PASS = credentials('dockerhub')
	}
	stages {

		stage("Check Workspace") {
            		steps {
                		sh 'pwd'  // Print current directory (workspace)
                		sh 'ls'   // List files in current directory (workspace)
            		}
        	}
		stage("Building the Student Survey Image"){
			steps{
				script{
					checkout scm
					sh 'cp index.html /var/www/html/'
					sh 'echo ${BUILD_TIMESTAMP}'
					//sh "sudo docker login -u johnstephengutam -p ${DOCKERHUB_PASS}"

					// Use Jenkins credentials binding to securely login to DockerHub
			                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
			                	//sh "docker login -u johnstephengutam -p ${DOCKERHUB_PASS}"
						sh "echo ${DOCKERHUB_PASS} | docker login -u johnstephengutam --password-stdin"
			                        
						def buildTimestamp = "${BUILD_TIMESTAMP}".replaceAll(/[^\w.-]/, '-')
						def customImageTag = "johnstephengutam/mywebapp:${buildTimestamp}"
						def customImage = docker.build(customImageTag)
			                }
				}
			}
		}
		stage("Pushing Image to DockerHub"){
			steps{
				script{
					sh "docker push johnstephengutam/mywebapp:${BUILD_TIMESTAMP}"
				}
			}
		}
		stage("Deploying to Rancher as single pod"){
			steps{
				sh 'kubectl set image deployment/hw2-deployment container-0=johnstephengutam/mywebapp:${BUILD_TIMESTAMP}'
			}
		}
	}
}
