pipeline {
    agent any
    tools {
        maven "Maven" 
	  
	    
    }
   stages {
   	/*stage("SonarQube Analysis"){
        	steps {
                	withSonarQubeEnv("Sonarqube") {
                    		sh "mvn -f pom.xml sonar:sonar -Dsonar.sources=src/"
                    		script {
		    			LAST_STARTED = env.STAGE_NAME
					container_Up = false
                    			timeout(time: 1, unit: "HOURS") { 
                        			sh "curl -u admin:admin -X GET -H \"Accept: application/json\" http://104.248.169.167:9000/api/qualitygates/project_status?projectKey=com.mycompany:jenkinsnewest > status.json"
                        			def json = readJSON file:"status.json"
                        			echo "${json.projectStatus}"
                        			if ("${json.projectStatus.status}" != "OK") {
                            				currentBuild.result = "FAILURE"
                           				error("Pipeline aborted due to quality gate failure.")
                           			}
                        		}     
                    		}
                	}
                }
	}*/
      
	 
	 stage('Build') {
            steps {
		    configFileProvider([configFile(fileId: "07f3e89d-0bd6-4385-9cb1-9a75ad55f621", variable: "settings")]){
				sh "mvn -f pom.xml -s $settings clean install -DskipTests "     
				}
            		
                  }    
        } 
        stage ('Munit Test'){
        	steps {
        		    sh "mvn -f pom.xml test"
        	      }    
        }
        stage('Functional Testing'){
        	steps {
        			sh "mvn -f pom.xml test -Dtestfile=src/test/javarunner.TestRunner.java"
             	  }
            }
        stage('Generate Reports') {
      		steps {
        		    cucumber(failedFeaturesNumber: -1, failedScenariosNumber: -1, failedStepsNumber: -1, fileIncludePattern: 'cucumber.json', jsonReportDirectory: 'target', pendingStepsNumber: -1, skippedStepsNumber: -1, sortingMethod: 'ALPHABETICAL', undefinedStepsNumber: -1)
                  }
            }
        stage('Archetype'){
        	steps {
                    sh "mvn -f pom.xml archetype:create-from-project"
                    sh "mvn -f target/generated-sources/archetype/pom.xml clean install"
                  } 
        	}    
        stage('Deploy to Cloudhub'){
        	steps {
        	    	sh "mvn -f pom.xml package deploy -DskipTests -Dusername=njcdemo1 -Dpassword=Njclabs@123 -DapplicationName=jenkinsnewest-poc -Denvironment=Sandbox -DmuleDeploy"
             	  }
            }
	   
    
}
}
