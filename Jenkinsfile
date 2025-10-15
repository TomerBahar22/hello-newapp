
def appname = "hello-newapp"
def repo = "TomerBahar22"  // Replace with your DockerHub username
def artifactory = "docker.io" 
def appimage = "docker.io/${repo}/${appname}"
def apptag = "${env.BUILD_NUMBER}"

podTemplate(containers: [
      containerTemplate(name: 'jnlp', image: 'jenkins/inbound-agent', ttyEnabled: true),
      containerTemplate(name: 'docker', image: 'gcr.io/kaniko-project/executor:debug-v0.19.0', command: "/busybox/cat", ttyEnabled: true)
  ],
  volumes: [
     configMapVolume(mountPath: '/kaniko/.docker/', configMapName: 'docker-cred')
  ])  {
    node(POD_LABEL) {
        stage('chackout') {
            container('jnlp') {
            sh '/usr/bin/git config --global http.sslVerify false'
	    checkout scm
          }
        } // end chackout

        stage('build') {
            container('docker') {
        		sh """
          			/kaniko/executor \
		            	--context="${WORKSPACE}" \
		            	--dockerfile="${WORKSPACE}/Dockerfile" \
		            	--destination="${appimage}:${apptag}" \
		           		--destination="${appimage}:latest" \
		            	--cache=true \
		            	--cache-dir=/cache \
		            	--verbosity=info
        		"""
            }
        } //end build

        stage('deploy') {
            container('docker') {
	      if (deploy) {
                echo "***** Doing some deployment stuff *********"
             }  else {
                echo "***** NO DEPLOY - Doing somthing else. Testing? *********"
             }
           }
        } //end deploy
    }
    /*
    post {
        always {
            echo 'Pipeline completed!'
        }
        success {
            echo 'Pipeline succeeded!'
            echo 'Send sucess email'
            echo 'Notify CMDB'
        }
        failure {
            echo 'Pipeline failed!'
            echo 'send error email'
        }
    }
    */
}
