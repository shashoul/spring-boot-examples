pipeline{
    
    agent {
      label 'k8s'
    }	

    stages{

        stage('Build/Test'){
            steps{
                sh "cd spring-boot-package-war && mvn -B versions:set -DnewVersion=${env.BUILD_NUMBER} &&  mvn clean package "
            }
	}
	stage('Show Tests result'){
  	    steps{ 		
	       junit '**/target/surefire-reports/*.xml'     
 	    }		
	}
	stage('Archive'){
            steps{
                archiveArtifacts artifacts: '**/target/*.war' 
            }	
        }   
        stage('Docker image'){
          steps{
              sh "cd spring-boot-package-war && docker build -t gcr.io/my-gke-205110/my-java-app:${env.BUILD_NUMBER} ."
          }
        }
	stage('Push image to google container'){
          steps{
              sh "gcloud docker -- push gcr.io/my-gke-205110/my-java-app:${env.BUILD_NUMBER} && \
                  cd spring-boot-package-war && \
                  sed -r -i 's/(my-java-app:)([0-9]+)/my-java-app:${env.BUILD_NUMBER}/g' app-deployment.yml && \
                  cat app-deployment.yml"
          } 
        }
        stage('Deploy image'){
          steps{
             sh "cd spring-boot-package-war && kubectl --namespace jenkins apply -f app-deployment.yml"   
          }
        } 
 
   }    
}
