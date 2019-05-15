    
pipeline {
    agent any
	
	parameters {
        string ( defaultValue: "1.0" ,description: 'Version Number to use ?', name : 'APPLICATION_VERSION')
    }

    stages {
      
       stage('Evaluate Changes') {
         steps {
              script {
                      jarFiles = findFiles(glob: 'plugins/*.jar')
                      sqlFiles = findFiles(glob: 'scripts/*.sql')
		      sh 'rm env.properties'
		      if(sqlFiles.size() > 0 ){
			      sh "echo '{\"SQL_CHANGED\":\"TRUE\"}' >> env.properties"
		      }
		      if(jarFiles.size() > 0 ){
			      sh "echo '{\"PLUGINS_CHANGED\":\"TRUE\"}' >> env.properties"
		      }
                      sh "echo '{\"APPLICATION_VERSION\":\"${APPLICATION_VERSION}\"}' >> env.properties"
		      archiveArtifacts artifacts: 'env.properties', fingerprint: true
               }
            }
       }
      
        stage('Final') {
            steps {
                script {
                    
                    jarFiles.each { appName ->
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

