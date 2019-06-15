/* This script is designed and maintained by Ganesh Palnitkar
*/
def notify(status) {
  mail (
        body:"""${status}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]':
                 Check console output at,
                 href='${env.BUILD_URL}'>${env.JOB_NAME} [${env.BUILD_NUMBER}]""",
        cc: '<valid-email-id>',
        subject: """JenkinsNotification: ${status}:""",
        to: '<shubhani.299@gmail.com>'
       )
 }
def server = Artifactory.server 'http://35.184.204.251:8081/artifactory/'
def uploadSpec = """{
  "files": [
	{
	  "pattern": "example-repo-local/Helloworldwebapp.war",
	  "target": "petclinic/"
	}
		   ]
  }"""

/*def downloadSpec = """{
 "files": [
  {
      "pattern": "petclinic/*.war",
      "target": "./"
    }
 ]
}"""
*/
pipeline {
 agent none
    stages {
      stage('SCM_Chekout') {
          agent { label "node1" }
			steps {
			    script {
					notify('build-started')
				}
                checkout([$class: 'GitSCM',
                    branches: [[name: '*/master']],
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [],
                    submoduleCfg: [],
                    userRemoteConfigs: [[url: 'https://github.com/shubhanij/helloworldweb.git']]])
            }
        }
      stage('Build'){
          agent { label "build1" }
			steps {
                sh 'mvn -f pom.xml clean package'
            }
        }

	  stage('push-to-artifactory') {
          agent { label "build1" }
			steps {
                script {
				   server.upload(uploadSpec)
            }
        }
	}
	  stage('Deploy') {
          agent { label "tomcatserver" }
			steps {
			    script {
				  input('Deploy Package to Production?')
				  notify('Deployment-to-Production')
				}
					sh 'wget http://35.184.204.251:8081/artifactory/webapp/#/artifacts/browse/tree/General/example-repo-local/Helloworldwebapp.war'
					sh 'cp ./Helloworldwebapp.war /opt/tomcat/webapps/'
					sh '/opt/tomcat/bin/catalina.sh run'

            }
         }
    }
}

