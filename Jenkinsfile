pipeline {
	agent any

	//Configure the following environment variables before executing the Jenkins Job
	environment {
		IntegrationFlowID = "test1"
		GetEndpoint = true //If you don't need the endpoint or the artefact does not provide an endpoint, set the value to false
                DeploymentCheckRetryCounter = 20 //multiply by 3 to get the maximum deployment time
		CPIHost = "${env.CPI_HOST}"
		CPIOAuthHost = "${env.CPI_OAUTH_HOST}"
		CPIOAuthCredentials = "${env.CPI_OAUTH_CRED}"
		GITRepositoryURL   = "${env.GIT_REPOSITORY_URL}"
		GITCredentials = "${env.GIT_CRED}"
		GITBranch = "${env.GIT_BRANCH_NAME}"
		GITComment = "Integration Artifacts update from CI/CD pipeline."
		GITFolder = "IntegrationArtifact"
	}
  
	stages {
	
    stage('Check and Upload new resource files to CPI tenant') {
      steps {
        script {
          //Check and upload new resource files to CPI tenant
          println '**** Check and upload new resource files to CPI tenant ****';
          println 'New Files: ' + new_files;
          
          def newFiles = readJSON text: new_files;
          //Loop all the new files and upload of type xslt,groovy,javascript or jar
          newFiles.each {item ->
            def pathArr = item.split('src/main/resources');
            if(pathArr.size() == 2){
              def iflowID = pathArr[0].split('/');
              iflowID = iflowID[iflowID.size()-1];
              def fileNameWithExtension = pathArr[1].split('/');
              fileNameWithExtension = fileNameWithExtension[fileNameWithExtension.size()-1];
              def fileNameArr = fileNameWithExtension.split('\\.');
              def fileExtension = fileNameArr.size()>=2 ? fileNameArr[fileNameArr.size()-1] : '';
              def resourceType = '';
              switch(fileExtension) {
                case 'xsl':
                  resourceType = 'xslt';
                  break;
                case 'gsh':
                  resourceType = 'groovy';
                  break;
                case 'groovy':
                  resourceType = fileExtension;
                  break;
                case 'js':
                  resourceType = fileExtension;
                  break;
                case 'jar':
                  resourceType = fileExtension;
                  break;
                default:
                  resourceType = '';
                  break;
              }
              if(resourceType != ''){
                def fileContent = readFile encoding: 'Base64', file: item;
                def postPayload = '{\"Name\": \"' + fileNameWithExtension + '\", \"ResourceType\": \"' + resourceType + '\", \"ResourceContent\": \"' + fileContent + '\"}';
                try {
                  def addResource = httpRequest acceptType: 'APPLICATION_JSON',
                      contentType: 'APPLICATION_JSON',
                      httpMode: 'POST',
                      ignoreSslErrors: true,
                      consoleLogResponseBody: true,
                      responseHandle: 'LEAVE_OPEN',
                      validResponseCodes: '200,201,202',
                      customHeaders: [
                        [maskValue: true, name: 'Authorization', value: env.token]
                      ],
                      timeout: 30,
                      requestBody: postPayload, 
                      url: "https://" + env.CPIHost + '/api/v1/IntegrationDesigntimeArtifacts(Id=\'' + iflowID + '\',Version=\'active\')/Resources';
                } catch (Exception e) {
                    error("Adding a resource to an integration flow failed");
                }
              }else{
                println("Added file is not of type 'xslt | groovy | js | jar'");
              }
            }
          }
        }
      }
    }

    stage('Check and Upload modified resource files to CPI tenant') {
      steps {
        script {
          //Check and upload modified resource files to CPI tenant
          println '**** Check and upload modified resource files to CPI tenant ****';
          println 'Modified Files: ' + modified_files;
          
          def modFiles = readJSON text: modified_files;
          //Loop all the modified files and upload of type xslt,groovy,javascript or jar
          modFiles.each {item ->
            def pathArr = item.split('src/main/resources');
            if(pathArr.size() == 2){
              def iflowID = pathArr[0].split('/');
              iflowID = iflowID[iflowID.size()-1];
              def fileNameWithExtension = pathArr[1].split('/');
              fileNameWithExtension = fileNameWithExtension[fileNameWithExtension.size()-1];
              def fileNameArr = fileNameWithExtension.split('\\.');
              def fileExtension = fileNameArr.size()>=2 ? fileNameArr[fileNameArr.size()-1] : '';
              switch(fileExtension) {
                case 'xsl':
                  resourceType = 'xslt';
                  break;
                case 'gsh':
                  resourceType = 'groovy';
                  break;
                case 'groovy':
                  resourceType = fileExtension;
                  break;
                case 'js':
                  resourceType = fileExtension;
                  break;
                case 'jar':
                  resourceType = fileExtension;
                  break;
                default:
                  resourceType = '';
                  break;
              }
              if(resourceType != ''){
                def fileContent = readFile encoding: 'Base64', file: item;
                def postPayload = '{\"ResourceContent\": \"' + fileContent + '\"}';
                try {
                  def updateResource = httpRequest acceptType: 'APPLICATION_JSON',
                      contentType: 'APPLICATION_JSON',
                      httpMode: 'PUT',
                      ignoreSslErrors: true,
                      consoleLogResponseBody: true,
                      responseHandle: 'LEAVE_OPEN',
                      validResponseCodes: '200,202',
                      customHeaders: [
                        [maskValue: true, name: 'Authorization', value: env.token]
                      ],
                      timeout: 30,
                      requestBody: postPayload, 
                      url: "https://" + env.CPIHost + '/api/v1/IntegrationDesigntimeArtifacts(Id=\'' + iflowID + '\',Version=\'active\')/$links/Resources(Name=\'' + fileNameWithExtension + '\',ResourceType=\'' + resourceType + '\')';
                } catch (Exception e) {
                    error("Updating a resource of an integration flow failed");
                }
              }else{
                println("Modified file is not of type 'xslt | groovy | js | jar'");
              }
            }
          }
        }
      }
    }
    
    stage('Check and Delete removed resource files to CPI tenant') {
      steps {
        script {
          //Check and Delete removed resource files to CPI tenant
          println '**** Check and Delete removed resource files to CPI tenant ****';
          println 'Deleted Files: ' + removed_files;
          
          def delFiles = readJSON text: removed_files;
          //Loop all the deleted files and upload of type xslt,groovy,javascript or jar
          delFiles.each {item ->
            def pathArr = item.split('src/main/resources');
            if(pathArr.size() == 2){
              def iflowID = pathArr[0].split('/');
              iflowID = iflowID[iflowID.size()-1];
              def fileNameWithExtension = pathArr[1].split('/');
              fileNameWithExtension = fileNameWithExtension[fileNameWithExtension.size()-1];
              def fileNameArr = fileNameWithExtension.split('\\.');
              def fileExtension = fileNameArr.size()>=2 ? fileNameArr[fileNameArr.size()-1] : '';
              def resourceType = '';
              switch(fileExtension) {
                case 'xsl':
                  resourceType = 'xslt';
                  break;
                case 'gsh':
                  resourceType = 'groovy';
                  break;
                case 'groovy':
                  resourceType = fileExtension;
                  break;
                case 'js':
                  resourceType = fileExtension;
                  break;
                case 'jar':
                  resourceType = fileExtension;
                  break;
                default:
                  resourceType = '';
                  break;
              }
              if(resourceType != ''){
                try {
                  def deleteResource = httpRequest acceptType: 'APPLICATION_JSON',
                      contentType: 'APPLICATION_JSON',
                      httpMode: 'DELETE',
                      ignoreSslErrors: true,
                      consoleLogResponseBody: true,
                      responseHandle: 'LEAVE_OPEN',
                      validResponseCodes: '200,201,202',
                      customHeaders: [
                        [maskValue: true, name: 'Authorization', value: env.token]
                      ],
                      timeout: 30,
                      url: "https://" + env.CPIHost + '/api/v1/IntegrationDesigntimeArtifacts(Id=\'' + iflowID + '\',Version=\'active\')/$links/Resources(Name=\'' + fileNameWithExtension + '\',ResourceType=\'' + resourceType + '\')';
                } catch (Exception e) {
                    error("Deleting a resource of an integration flow failed");
                }
              }else{
                println("Deleted file is not of type 'xslt | groovy | js | jar'");
              }
            }
          }
          //Delete workspace
          deleteDir()
        }
      }
    }
		stage('store new versions in Git') {
			steps {
				//clean up workspace first
				deleteDir();
				script {
					//Synchronize repository to workspace
					checkout([
						$class: 'GitSCM',
						branches: [[name: env.GITBranch]],
						doGenerateSubmoduleConfigurations: false,
						extensions: [
							[$class: 'RelativeTargetDirectory',relativeTargetDir: "."],
							[$class: 'SparseCheckoutPaths',  sparseCheckoutPaths:[[$class:'SparseCheckoutPath', path: env.GITFolder]]]
						],
						submoduleCfg: [],
						userRemoteConfigs: [[
							credentialsId: env.GITCredentials,
							url: 'https://' + env.GITRepositoryURL 
						]]
					])
					
					//get Cloud Integration Oauth token
					def cpiTokenResponse = httpRequest acceptType: 'APPLICATION_JSON', 
						authentication: env.CPIOAuthCredentials, 
						ignoreSslErrors: false, 
						responseHandle: 'LEAVE_OPEN', 
						timeout: 30, 
						url: 'https://' + env.CPIOAuthHost + '/oauth/token?grant_type=client_credentials';
					def jsonObj = readJSON text: cpiTokenResponse.content
					def cpiToken = 'bearer' + ' ' + jsonObj.access_token;
					cpiTokenResponse.close();
					
					//get flow version from Cloud Integration tenant
					def cpiVersionResponse = httpRequest acceptType: 'APPLICATION_JSON', 
						customHeaders: [[maskValue: true, name: 'Authorization', value: cpiToken]],
						ignoreSslErrors: false, 
						responseHandle: 'LEAVE_OPEN', 
						validResponseCodes: '200:299,404',
						timeout: 30, 
						url: 'https://' + 'f46f9be9trial.it-cpitrial05.cfapps.us10-001.hana.ondemand.com' + '/api/v1/IntegrationDesigntimeArtifacts(Id=\'' + env.IntegrationFlowID + '\',Version=\'active\')';
					if (cpiVersionResponse.status == 404){
						//Flow not found
						println("received http 404. Please check the Flow ID you provided in the script. Looks like it does not exist on the tenant.");
						cpiVersionResponse.close();
						currentBuild.result = 'FAILURE';
						return;
					}
					jsonObj = readJSON text: cpiVersionResponse.content;
					def cpiVersion = jsonObj.d.Version;
					println("Flow version on Cloud Integration tenant: '" + cpiVersion + "'");
					cpiVersionResponse.close();
					
					//get version from source repository 
					def git_version;
					if (fileExists(env.GITFolder + '/' + env.IntegrationFlowID)){
						dir(env.GITFolder + '/' + env.IntegrationFlowID + "/META-INF"){
							def manifestFile = readFile(file: 'MANIFEST.MF');
							def index = manifestFile.indexOf('Bundle-Version:')+16;
							def lastindex = manifestFile.indexOf('\n', index);
							git_version = manifestFile.substring(index, lastindex);
							println("Flow version in Git: '" + git_version + "'");
						}
						//now compare the versions
						if (git_version.trim().equalsIgnoreCase(cpiVersion.trim())){
							println("Versions are same, no action required");
							//EXIT
							currentBuild.result = 'SUCCESS';
							return
						} else {
							println("Versions are different");
						}	
					} else {
						println("Flow does not exist in Git yet");
					}

					//delete the old flow content from the workspace so that only the latest flow version is stored
					dir(env.GITFolder + '/' + env.IntegrationFlowID){
						deleteDir();
					}
					
					//download and extract latest integration flow version from Cloud Integration tenant
					def tempfile=UUID.randomUUID().toString() + ".zip";   
					println("Download artefact");
					def cpiFlowResponse = httpRequest acceptType: 'APPLICATION_ZIP', 
						customHeaders: [[maskValue: true, name: 'Authorization', value: cpiToken]],
						ignoreSslErrors: false, 
						responseHandle: 'LEAVE_OPEN', 
						timeout: 30,  
						outputFile: tempfile,
						url: 'https://' + 'f46f9be9trial.it-cpitrial05.cfapps.us10-001.hana.ondemand.com' + '/api/v1/IntegrationDesigntimeArtifacts(Id=\''+ env.IntegrationFlowID + '\',Version=\'active\')/$value';
					def disposition = cpiFlowResponse.headers.toString();
					def index=disposition.indexOf('filename')+9;
					def lastindex=disposition.indexOf('.zip', index);
					def filename=disposition.substring(index + 1, lastindex + 4);
					def folder=env.GITFolder + '/' + filename.substring(0, filename.indexOf('.zip'));
					println("Unzip artefact");
					fileOperations([fileUnZipOperation(filePath: tempfile, targetLocation: folder)])
					cpiFlowResponse.close();

					//remove the zip
					fileOperations([fileDeleteOperation(excludes: '', includes: tempfile)])

					//adding the content to the index and uploading it to the repository
					dir(folder){
						sh 'git add .'
					}
					println("Store artefact in Git");
					withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: env.GITCredentials ,usernameVariable: 'GitUserName', passwordVariable: 'GitPassword']]) {    
						sh 'git diff-index --quiet HEAD || git commit -am ' + '\'' + env.GITComment + '\''
						sh('git push https://${GitUserName}:${GitPassword}@' + env.GITRepositoryURL  + ' HEAD:' + env.GITBranch)
                    }
                
				}
        	}
      	}
   
		 stage('Generate oauth bearer token') {
      steps {
        script {
          //get oauth token for Cloud Integration
          println("requesting oauth token");
          def getTokenResp = httpRequest acceptType: 'APPLICATION_JSON',
            authentication: "${env.CPIOAuthCredentials}",
            contentType: 'APPLICATION_JSON',
            httpMode: 'POST',
            responseHandle: 'LEAVE_OPEN',
            timeout: 30,
            url: 'https://' + env.CPIOAuthHost + '/oauth/token?grant_type=client_credentials';
          def jsonObjToken = readJSON text: getTokenResp.content
          def token = "Bearer " + jsonObjToken.access_token
          env.token = token
          getTokenResp.close();
        }
      }
    }

    stage('Deploy integration flow and check for deployment success') {
      steps {
        script {
          //deploy integration flow as specified in the configuration
          println("Deploying integration flow.");
          def deployResp = httpRequest httpMode: 'POST',
            customHeaders: [
              [maskValue: false, name: 'Authorization', value: env.token]
            ],
            ignoreSslErrors: true,
            timeout: 30,
            url: 'https://' + 'f46f9be9trial.it-cpitrial05.cfapps.us10-001.hana.ondemand.com' + '/api/v1/DeployIntegrationDesigntimeArtifact?Id=\'' + "${env.IntegrationFlowID}" + '\'&Version=\'active\'';
          //check deployment status
          println("Start checking integration flow deployment status.");
          Integer counter = 0;
          def deploymentStatus;
          def continueLoop = true;
		  
		      //check until max check counter reached or we have a final state
          while (counter < env.DeploymentCheckRetryCounter.toInteger() & continueLoop == true) {
            Thread.sleep(3000);
            counter = counter + 1;
            def statusResp = httpRequest acceptType: 'APPLICATION_JSON',
              customHeaders: [
                [maskValue: false, name: 'Authorization', value: env.token]
              ],
              httpMode: 'GET',
              responseHandle: 'LEAVE_OPEN',
              timeout: 30,
              url: 'https://' + "${env.CPIHost}" + '/api/v1/IntegrationRuntimeArtifacts(\'' + "${env.IntegrationFlowID}" + '\')';
            def jsonObj = readJSON text: statusResp.content;
            deploymentStatus = jsonObj.d.Status;

            println("Deployment status: " + deploymentStatus);
			
            if (deploymentStatus.equalsIgnoreCase("Error")) {
              //in case of error, get the error details
              def deploymentErrorResp = httpRequest acceptType: 'APPLICATION_JSON',
                customHeaders: [
                  [maskValue: false, name: 'Authorization', value: env.token]
                ],
                httpMode: 'GET',
                responseHandle: 'LEAVE_OPEN',
                timeout: 30,
                url: 'https://' + "${env.CPIHost}" + '/api/v1/IntegrationRuntimeArtifacts(\'' + "${env.IntegrationFlowID}" + '\')' + '/ErrorInformation/$value';
              def jsonErrObj = readJSON text: deploymentErrorResp.content
              def deployErrorInfo = jsonErrObj.parameter;
              println("Error Details: " + deployErrorInfo);
              statusResp.close();
              deploymentErrorResp.close();
              //End the whole job
              sh 'exit 1';
            } else if (deploymentStatus.equalsIgnoreCase("Started")) {
			        //Final status reached 
              println("Integration flow deployment successful")
              statusResp.close();
              continueLoop = false
            } else {
			        //Continue checking 
              println("The integration flow is not yet started. Will wait 3s and then check again.")
            }
          }
		      //After exiting the loop, react to the deployment state
          if (!deploymentStatus.equalsIgnoreCase("Started")) {
		        //If status not is Started, end the pipeline.
            println("No final deployment status reached. Current status: \'" + deploymentStatus);
            sh 'exit 1';
          } else {
            if (env.GetEndpoint.equalsIgnoreCase("true")) {
              //Get endpoint as configured above
              def endpointResp = httpRequest acceptType: 'APPLICATION_JSON',
                customHeaders: [
                  [maskValue: false, name: 'Authorization', value: env.token]
                ],
                httpMode: 'GET',
                responseHandle: 'LEAVE_OPEN',
                timeout: 30,
                url: 'https://' + "${env.CPIHost}" + '/api/v1/ServiceEndpoints?$filter=Name%20eq%20\'' + "${env.IntegrationFlowID}" + '\'&$select=EntryPoints&$format=json&$expand=EntryPoints'
              def jsonEndpointObj = readJSON text: endpointResp.content;
              def endpoint = jsonEndpointObj.d.results.EntryPoints.results.Url;
              def size = (jsonEndpointObj.d.results.EntryPoints.results).size();
              endpointResp.close();
			        //check if the flow has an endpoint
              if (size != 0) {
                println("Endpoint: " + endpoint);
              } else {
                unstable("The specified integration flow does not have an endpoint. Please check the flow or tenant")
              }
            }
          }
        }
      }
    }
  
	
	}
	
	
	
}
