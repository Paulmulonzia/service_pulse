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
	def url
        stage('DeployToStaging') {
            steps {
		node('prod_server'){
	          echo 'Change staging localhost directory permissions'
                  sh 'sudo chown ubuntu -hR /var/www/flask'
		  echo 'Fetch staging server public IP'
		  url = sh(script: "dig +short myip.opendns.com @resolver1.opendns.com", returnStdout: true,)
                }
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
        stage('Application Smoke Test') {
            steps {
              node('staging_server'){
                echo 'Application Smoke test'
                sh 'curl -Is localhost | head -1'
		echo '${url}'
                }
            }
        }
        stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {
                input 'Does the staging environment look OK?'
		echo 'Change localhost directory permissions'
		node('prod_server'){
                  sh 'sudo chown ubuntu -hR /var/www/flask'
                }
                echo 'deploy flask app'
                    sshPublisher(
                        failOnError: true,
                        continueOnError: false,
                        publishers: [
                            sshPublisherDesc(
                                configName: 'prod',
                                verbose: true, 
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
	stage('Production Application Smoke Test') {
            steps {
              node('prod_server'){
                echo 'Application Smoke test'
                sh 'curl -Is localhost | head -1'
                }
            }
        }

    }
}
