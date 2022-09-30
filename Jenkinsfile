pipeline {
	    agent any
	

	    // Declare Environment Variables
	    environment {
	        	//Orchestrator Services
				UIPATH_ORCH_URL = "https://cloud.uipath.com/"
	            UIPATH_ORCH_LOGICAL_NAME = "aitraining"
	            UIPATH_ORCH_TENANT_NAME = "MaiLe_Sep30_TestSuite"
	            UIPATH_ORCH_FOLDER_NAME = "MaiLe" // replace this variable with your UiPath folder name
			    EXAPP_APPID = "76eb4f99-5f00-4dfd-b8d0-46a171cfc53d"
			    EXAPP_SCOPES = "OR.Assets OR.BackgroundTasks OR.Execution OR.Folders OR.Jobs OR.Machines.Read OR.Monitoring OR.Robots.Read OR.Settings.Read OR.TestSetExecutions OR.TestSets OR.TestSetSchedules OR.Users.Read"
			    EXAPP_IDENTITYURL = "https://cloud.uipath.com/aitraining"
	    }
	

	    stages {
	        stage('Preparing'){
	            steps {
	                echo "Jenkins Home ${env.JENKINS_HOME}"
	                echo "Jenkins URL ${env.JENKINS_URL}"
	                echo "Jenkins JOB Number ${env.BUILD_NUMBER}"
	                echo "Jenkins JOB Name ${env.JOB_NAME}"
	                echo "GitHub BranchName ${env.BRANCH_NAME}"
	                checkout scm
		    	}
	        }	
	    
			// UiPath Run Tests
	        stage('Unit Tests') {
	            steps {
	            echo 'Testing the workflow...'
	            }
			}    
		    
			// UiPath Pack
	        stage('Build Nuget Package') {
				when {
					expression {
						currentBuild.result == null || currentBuild.result == 'SUCCESS' 
					}
				}				
				steps {
					echo "Building package with ${WORKSPACE}"
				}
	        }
			
	        // UiPath Deploy
	        stage('Deploy To Orchestrator') {
				when {
					expression {
						currentBuild.result == null || currentBuild.result == 'SUCCESS' 
						}
				}
				steps {
	                echo 'Deploying process to orchestrator...'
				}
			}
	    }
	

	    // Options
	    options {
	        // Timeout for pipeline
	        timeout(time:80, unit:'MINUTES')
	        skipDefaultCheckout()
	    }


	    // post actions
	    post {
	        success {
	            echo 'Deployment has been completed!'
	        }
	        failure {
	          echo "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.JOB_DISPLAY_URL})"
	        }
			always {
				// clean up workspace
				deleteDir()
	        }
	    }
}
