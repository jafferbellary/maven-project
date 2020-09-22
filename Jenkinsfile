pipeline {
    agent any
    tools{
	   maven "Maven"
    } 
     stages {
         stage("Build") {
             steps {
                 echo "Building an application using maven"
				sh 'mvn clean package'
             }
	     post{
			success{
				echo "Artifact is generated successfully"
				echo "Archiving an artifact"
				archiveArtifacts artifacts: '**/*.war', followSymlinks: false
			}
			failed{
		      echo "Failed to generate an artifact"
			}
	     }
         }
         
     }
     stage("Deploying to staging and perform code analysis"){
		parallel{
			stage("Deploy to staging"){
				steps{
					deploy adapters: [tomcat8(credentialsId: 'e96f8189-33d4-4334-b69f-c0e4e00847eb', path: '', url: 'http://ec2-3-137-181-148.us-east-2.compute.amazonaws.com:8888/')], contextPath: null, onFailure: false, war: '**/*.war'
				}
				post{
					success{
						echo "Application is deployed successfully on Tomcat in staging environment"	
					}
					failure{
						echo "Failed to deploy an application in staging environment"
					}
				}
			}
			
			stage("Perform code analysis"){
				steps{
					sh 'mvn checkstyle:checkstyle'
				}
				post{
					success{
						checkstyle canComputeNew: false, defaultEncoding: '', healthy: '', pattern: '', unHealthy: ''
					}
				}
			}
		}
	 }
	 
	 stage("Deploy to production"){
		steps{
			timeout(time: 1, unit: 'DAYS') {
			input 'Do you want to deploy an application to production'
			}
			
			deploy adapters: [tomcat8(credentialsId: 'e96f8189-33d4-4334-b69f-c0e4e00847eb', path: '', url: 'http://ec2-3-137-181-148.us-east-2.compute.amazonaws.com:9999/')], contextPath: null, onFailure: false, war: '**/*.war'
		}
	 }
	 post{
		success{
			echo "Application is deployed successfully on Tomcat in production environment"	
		}
		failure{
			echo "Failed to deploy an application in production environment"
		}
	 }
 }