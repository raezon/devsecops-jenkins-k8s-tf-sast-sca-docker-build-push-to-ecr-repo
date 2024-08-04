pipeline {
  agent any

  tools { 
        maven 'Maven_3_5_2'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=PROJECT_KEY -Dsonar.organization=ORGANISATION -Dsonar.host.url=https://sonarcloud.io -Dsonar.token=1cc2143703da2f6a000facf8727c484101a82c77'
			}
    }

	stage('RunSCAAnalysisUsingSnyk') {
            steps {		
		   withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
					sh 'mvn snyk:test -fn'
				}
			}
    }		

	stage('Build') { 
            steps { 
               withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
                 script{
                 app =  docker.build("asg")
                 }
               }
            }
    }

	stage('Push') {
            steps {
                script{
                    docker.withRegistry('ECR_URL', 'ecr:eu-central-1:aws-credentials') {
                    app.push("latest")
                    }
                }
            }
    	}
	    
  }
}
