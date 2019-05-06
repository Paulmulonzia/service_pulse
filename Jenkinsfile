pipeline {
    agent any
    stages {
	
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
