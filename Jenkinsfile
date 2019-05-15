    
pipeline {
    agent any
	
	parameters {
        string ( defaultValue: "1.0" ,description: 'Version Number to use ?', name : 'APPLICATION_VERSION')
    }

    stages {
      
       stage('Evaluate Changes') {
         steps {
              script {
                      def jarFiles = findFiles(glob: 'plugins/*.jar')
                      def sqlFiles = findFiles(glob: 'scripts/*.sql')
		      sh 'rm -f env.properties'
		      sh "echo '{' >> env.properties"
		      if(sqlFiles.size() > 0 ){
			      sh "echo '\"SQL_CHANGED\":\"TRUE\",' >> env.properties"
		      }else{
			      sh "echo '\"SQL_CHANGED\":\"FALSE\",' >> env.properties"
		      }
		      if(jarFiles.size() > 0 ){
			      sh "echo '\"PLUGINS_CHANGED\":\"TRUE\",' >> env.properties"
		      }else{
			      sh "echo '\"PLUGINS_CHANGED\":\"FALSE\",' >> env.properties"
		      }
		      plugins = []
		      pluginNames=''
		      for (int i = 0; i < jarFiles.size(); ++i) {
			      pluginName = jarFiles[i].name.substring(0, jarFiles[i].name.lastIndexOf('.'))
			      sh "echo pluginName ${pluginName}"
			      pluginNames = pluginNames + pluginName;
			      if(i != jarFiles.size() -1){
				      pluginNames = pluginNames + ",";
			      }
			      plugins.add(pluginName)
                      }
                      sh "echo '\"APPLICATION_VERSION\":\"${APPLICATION_VERSION}\",' >> env.properties"
		      sh "echo '\"PLUGINS\":\"${pluginNames}\"' >> env.properties"
		      sh "echo '}' >> env.properties"
		      archiveArtifacts artifacts: 'env.properties', fingerprint: true
               }
            }
       }
      
        stage('Final') {
            steps {
                script {
                    
                    plugins.each { appName ->
                      stage("Publish to XL-Deploy ${appName}") {
                				script {APPLICATION_VERSION = params.APPLICATION_VERSION}
								sh "base-cp deployit-manifest.xml deployit-manifest-${appName}.xml"
								sh "sed -i -e 's/APPLICATION_VERSION/${APPLICATION_VERSION}/g' deployit-manifest-${appName}.xml"
								sh "sed -i -e 's/APPLICATION_NAME/${appName}/g' deployit-manifest-${appName}.xml"
								xldCreatePackage artifactsPath: 'plugins', manifestPath: "deployit-manifest-${appName}.xml", darPath: "xldeploy-${appName}.dar"
                				//xldPublishPackage serverCredentials: 'xldeploy', darPath: "xldeploy-${appName}.dar"
                        }
                    }
                }
            }
        }
	    
	stage('Clean plugins folder') {
		when {
                expression { plugins.size() != 0 }
            	}
         	steps {
			sshagent (credentials: ['sandeep-rawat-xebia-git']) {
         	     	sh "git checkout master"
	            	sh "git rm plugins/*.jar"
		        sh "git add ."
		        sh "git commit -m '[Jenkins] clean plugin folder'"
		        sh "git push origin master"
         		}
	 	}
	}
	    
	    
    }
}

