pipeline {
	    agent any
	
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

            // --------------------- Dev stages ----------------------------------
	        stage('DEV Stages') {
			    when { 
				    branch 'dev'
                }

                environment {
	                    MAJOR = '1'
	                    MINOR = '1'

                        //DEV Orchestrator Services
	                    UIPATH_ORCH_URL = "https://cloud.uipath.com/"
	                    UIPATH_ORCH_LOGICAL_NAME = "maipdicsvt"
	                    UIPATH_ORCH_TENANT_NAME = "DefaultTenant"
	                    UIPATH_ORCH_FOLDER_NAME = "Demos/Dev"          
                } 

                stages {

                    // UiPath Run tests
                    stage('Dev Unit Testing') {
	                    steps {
	                        echo 'Start running Unit Tests...'
				            UiPathTest (
					            testTarget: [$class: 'TestProjectEntry', testProjectPath: "${WORKSPACE}", environments: ""],
					            orchestratorAddress: "${UIPATH_ORCH_URL}",
					            orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
					            folderName: "${UIPATH_ORCH_FOLDER_NAME}",
					            timeout: 10000,
					            traceLevel: 'None',
					            testResultsOutputPath: '',
					            credentials: Token(accountName: "${UIPATH_ORCH_LOGICAL_NAME}", credentialsId: 'APIUserKey'),
					            parametersFilePath: ''
				            )
	                    }
                    }
                }
		    }
            

            // --------------------- UAT stages ----------------------------------
	        stage('UAT Stages') {
			    when { 
				    branch pattern: "release-\\d+(.*)", comparator: "REGEXP"
                }

                environment {
	                    MAJOR = '1'
	                    MINOR = '1'

                        //UAT Orchestrator Services
	                    UIPATH_ORCH_URL = "https://cloud.uipath.com/"
	                    UIPATH_ORCH_LOGICAL_NAME = "maipdicsvt"
	                    UIPATH_ORCH_TENANT_NAME = "DefaultTenant"
	                    UIPATH_ORCH_FOLDER_NAME = "Demos/UAT"          
                }                

                stages {
                    // UiPath Run tests
                    stage('UAT Unit Testing') {
	                    steps {
	                        echo 'Start running Unit Tests...'
				            UiPathTest (
					            testTarget: [$class: 'TestProjectEntry', testProjectPath: "${WORKSPACE}", environments: ""],
					            orchestratorAddress: "${UIPATH_ORCH_URL}",
					            orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
					            folderName: "${UIPATH_ORCH_FOLDER_NAME}",
					            timeout: 10000,
					            traceLevel: 'None',
					            testResultsOutputPath: '',
					            credentials: Token(accountName: "${UIPATH_ORCH_LOGICAL_NAME}", credentialsId: 'APIUserKey'),
					            parametersFilePath: ''
				            )
	                    }
                    }

                    // UiPath Pack
	                stage('Build Process') {
			            when {
					        expression {
						        currentBuild.result == null || currentBuild.result == 'SUCCESS'
					        }
				        }
			            steps {
				            echo "Building package with ${WORKSPACE}"
				            UiPathPack (
					            outputPath: "Output\\${env.BUILD_NUMBER}",
                                outputType: "Process",
					            projectJsonPath: "project.json",
					            version: [$class: 'ManualVersionEntry', version: "${MAJOR}.${MINOR}.${env.BUILD_NUMBER}"],
					            useOrchestrator: false,
					            traceLevel: 'Information'
						    )
				        }
	                }

	                // Deploy to Production Step
	                stage('Deploy Process') {
			            when {
					        expression {
						        currentBuild.result == null || currentBuild.result == 'SUCCESS'
					        }
			            }
			            steps {
	                	    echo 'Deploying process to orchestrator...'
	                	    UiPathDeploy (
	                		    packagePath: "Output\\${env.BUILD_NUMBER}",
	                		    orchestratorAddress: "${UIPATH_ORCH_URL}",
	                		    orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
	               			    folderName: "${UIPATH_ORCH_FOLDER_NAME}",
					            environments: "env",
					            createProcess: "Automatically create/update process",
					            credentials: Token(accountName: "${UIPATH_ORCH_LOGICAL_NAME}", credentialsId: 'APIUserKey'),
					            traceLevel: 'Information',
					            entryPointPaths: 'Main.xaml'
				            )
			            }
		            }
                }
            }


            // --------------------- PROD stages ----------------------------------
	        stage('PROD Stages') {
			    when { 
				    branch 'main'
                }

                environment {
	                    MAJOR = '1'
	                    MINOR = '1'

                        //PROD Orchestrator Services
	                    UIPATH_ORCH_URL = "https://cloud.uipath.com"
	                    UIPATH_ORCH_LOGICAL_NAME = "aitraining"
	                    UIPATH_ORCH_TENANT_NAME = "MaiLe_Sep30_TestSuite"
	                    UIPATH_ORCH_FOLDER_NAME = "MaiLe"
			    EXAPP_APPID = "76eb4f99-5f00-4dfd-b8d0-46a171cfc53d"
			    EXAPP_SCOPES = "OR.Assets OR.BackgroundTasks OR.Execution OR.Folders OR.Jobs OR.Machines.Read OR.Monitoring OR.Robots.Read OR.Settings.Read OR.TestSetExecutions OR.TestSets OR.TestSetSchedules OR.Users.Read"
			    EXAPP_IDENTITYURL = "https://cloud.uipath.com/aitraining"
                }                

                stages {
                    // UiPath Pack
	                stage('Prod Build Process') {
			            when {
					        expression {
						        currentBuild.result == null || currentBuild.result == 'SUCCESS'
					        }
				        }
			            steps {
				            echo "Building package with ${WORKSPACE}"
				            UiPathPack (
					            outputPath: "Output\\${env.BUILD_NUMBER}",
                                outputType: "Process",
					            projectJsonPath: "project.json",
					            version: [$class: 'ManualVersionEntry', version: "${MAJOR}.${MINOR}.${env.BUILD_NUMBER}"],
					            useOrchestrator: false,
					            traceLevel: 'Information'
						    )
				        }
	                }

	                // UiPath Deploy
	                stage('Prod Deploy Process') {
			            when {
					        expression {
						        currentBuild.result == null || currentBuild.result == 'SUCCESS'
					        }
			            }
			            steps {
	                	    echo 'Deploying process to orchestrator...'
	                	    UiPathDeploy (
	                		    packagePath: "Output\\${env.BUILD_NUMBER}",
	                		    orchestratorAddress: "${UIPATH_ORCH_URL}",
	                		    orchestratorTenant: "${UIPATH_ORCH_TENANT_NAME}",
	               			    folderName: "${UIPATH_ORCH_FOLDER_NAME}",
					            environments: "env",
					            createProcess: "Automatically create/update process",
					            credentials: ExternalApp(accountForApp: "${UIPATH_ORCH_LOGICAL_NAME}", applicationId: "${EXAPP_APPID}", applicationSecret: 'ExApp_aitraining_Mai_TestSuiteTraining', applicationScope: "${EXAPP_SCOPES}", identityUrl: "${EXAPP_IDENTITYURL}"),
					            traceLevel: 'Information',
					            entryPointPaths: 'Main.xaml'
				            )
			            }
		            }
                }
            }
	    }
	

	    // Options
	    options {
	        // Timeout for pipeline
	        timeout(time:80, unit:'MINUTES')
	        skipDefaultCheckout()
	    }
	

	

	    // 
	    post {
	        success {
	            echo 'Deployment has been completed!'
	        }
	        failure {
	          echo "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.JOB_DISPLAY_URL})"
	        }
		always {
	            /* Clean workspace if success */
	            deleteDir()
	        }
	    }
}
