pipeline{
	agent any
	environment {
	IMAGE_NAME = "docker.io/sheshivr1981/mbapp:v"
	}
        triggers {
		  pollSCM '* * * * *'
        }

	stages{
		stage("SCM"){	
			steps{
				echo "****Connect to Github*********"
				git credentialsId: 'GITHUB', url: 'https://github.com/sreddy2809/testapp.git'
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
			  withCredentials([usernamePassword(credentialsId: 'DOCKERREG', passwordVariable: 'DPASSWD', usernameVariable: 'DUSERNAME')]) {
                          sh """
							docker login -u ${DUSERNAME} -p ${DPASSWD}
							docker push ${IMAGE_NAME}${BUILD_NUMBER}
						  """
					}
			
			}
		}
		stage("Deploy"){
			steps{
					withCredentials([sshUserPrivateKey(credentialsId: 'K8scluster', keyFileVariable: 'OSPKEY', usernameVariable: 'OSUSER')]) {
                   sh """
				    sed -i "s/image_rep/${IMAGE_NAME}${BUILD_NUMBER}/g" deployment.yml
                    cat deployment.yml
                    scp -i ${OSPKEY} -o StrictHostKeyChecking=no ${OSUSER}@18.143.92.18:/home/ubuntu 					
					ssh -i ${OSPKEY} -o StrictHostKeyChecking=no ${OSUSER}@18.143.92.18 kubectl apply -f deployment.yml
				   """
				}			
				
				
			}
		}
		
	}
}
