pipeline{
	agent any
	environment {
	IMAGE_NAME = "docker.io/sheshivr1981/nbapptest:v"
	}

	stages{
		stage("SCM"){	
			steps{
				echo "****Connect to Github*********"
				git credentialsId: 'GITHUBACCESS', url: 'https://github.com/sreddy2809/testapp.git'
	            sh """
				  ls -l 
				"""
			}
		}
		stage("Build"){	
			steps{
				sh """
				  docker info
				  docker build -t ${IMAGE_NAME}${BUILD_NUMBER} .
				  docker images
				"""
			}
		}
		stage("ImagePush"){	
			steps{
			  withCredentials([usernamePassword(credentialsId: 'DOCKERHUB', passwordVariable: 'DPASSWD', usernameVariable: 'DUSERNAME')]) {
                          sh """
							docker login -u ${DUSERNAME} -p ${DPASSWD}
							docker push ${IMAGE_NAME}${BUILD_NUMBER}
						  """
					}
			
			}
		}
		stage("Deploy"){
			steps{
					withCredentials([sshUserPrivateKey(credentialsId: 'APPSERVER', keyFileVariable: 'OSPKEY', usernameVariable: 'OSUSER')]) {
                   sh """
					ssh -i ${OSPKEY} -o StrictHostKeyChecking=no ${OSUSER}@122.248.211.243 docker run -d ${IMAGE_NAME}${BUILD_NUMBER}
ssh -i ${OSPKEY} -o StrictHostKeyChecking=no ${OSUSER}@122.248.211.243 docker ps -a
				   """
				}			
				
				
			}
		}
		
	}
}
