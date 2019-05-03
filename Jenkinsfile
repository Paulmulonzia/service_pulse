pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
		echo 'Running build automation'
                sh 'sudo /etc/init.d/apache2 start -y'
            }
        }
        stage('Post-build Test') {
            steps {
		echo 'Checking for Syntax errors'
		sh 'python -m py_compile init.py'
            }
        }
	stage('DeployToStaging') {
	
            steps {
		echo 'deploy flask app'
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'staging',
				verbose: true,
                                transfers: [
                                    sshTransfer(
                                        sourceFiles: 'requirements.txt',
                                        remoteDirectory: '/var/www/flask',
                                        execCommand: 'sudo chown ubuntu /var/www/flask && sudo /etc/init.d/apache2 restart -y'
                                    )
                                ]
                            )
                        ]
                    )
            }
	}

	stage('Application Smoke Test') {
            steps {
	      node('staging_server'){
                echo 'Application Smoke test'
                sh 'curl -Isf localhost | head -1'
		}
            }
        }

	

    }
}
