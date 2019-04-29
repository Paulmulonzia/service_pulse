pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
		echo 'Running build automation'
                sh 'sudo /etc/init.d/apache2 start -y'
            }
        }
        stage('Pre-build Test') {
            steps {
		echo 'Checking for Syntax errors'
		sh 'python -m py_compile init.py'
            }
        }
	stage('DeployToStaging') {
	
            steps {
	      node('staging_server'){
                echo 'deploy flask app'
                withCredentials([usernamePassword(credentialsId: 'webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'staging',
                                sshCredentials: [
                                    username: "$USERNAME",
                                    encryptedPassphrase: "$USERPASS"
                                ], 
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'init.py',
                                        remoteDirectory: '/var/www/flask',
                                        execCommand: 'sudo /etc/init.d/apache2 restart -y'
                                    )
                                ]
                            )
                        ]
                    )
                }
            }
	}
      }
   }

	stage('Post-build Test') {
            steps {
	      node('staging_server'){
                echo 'Application Smoke test'
                sh 'curl -Is localhost | head -1'
		}
            }
        }


    }
}

