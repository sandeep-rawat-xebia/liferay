    
pipeline {
    agent { label 'jdeveloper' }
	
	parameters {
        string ( defaultValue: "1.0" ,description: 'Version Number to use ?', name : 'APPLICATION_VERSION')
    }

    stages {
      
       stage('Evaluate Changes') {
         steps {
              script {
                      	jarFiles = sh(script: 'ls plugins|grep jar', returnStdout: true).split()
                		sqlFiles = sh(script: 'ls scripts|grep sql', returnStdout: true).split()
                		archiveArtifacts artifacts: 'env.properties', fingerprint: true
                		sh "echo '{\"APPLICATION_VERSION\":\"${APPLICATION_VERSION}\"}' >> env.properties"
               }
            }
       }
      
        stage('Final') {
            steps {
                script {
                    
                 	if (params.PLUGINS == 'ALL') {
             			appNames = allAppNames;
            		  }else{
              			appNames =  params.PLUGINS.split(",")
            		}
                    
                    appNames.each { appName ->
                      stage("Publish to XL-Deploy ${appName}") {
                				script {APPLICATION_VERSION = params.APPLICATION_VERSION}
								sh "cp deployit-manifest.xml deployit-manifest-${appName}.xml"
								sh "sed -i -e 's/APPLICATION_VERSION/${APPLICATION_VERSION}/g' deployit-manifest-${appName}.xml"
								sh "sed -i -e 's/APPLICATION_NAME/${appName}/g' deployit-manifest-${appName}.xml"
								//xldCreatePackage artifactsPath: '.', manifestPath: "deployit-manifest-${appName}.xml", darPath: "xldeploy-${appName}.dar"
                				//xldPublishPackage serverCredentials: 'xldeploy', darPath: "xldeploy-${appName}.dar"
                        }
                    }
                }
            }
        }
    }
}

