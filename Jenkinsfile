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
                echo 'deploy flask app'
                sh 'sudo cp init.py /var/www/flask'
		sh 'sudo /etc/init.d/apache2 restart -y'
            }
        }
	stage('Post-build Test') {
            steps {
                echo 'Application Smoke test'
                def output = sh(returnStdout: true, script: 'curl -Is localhost | head -1')
		println "output = ${output}"
            }
        }


    }
}

