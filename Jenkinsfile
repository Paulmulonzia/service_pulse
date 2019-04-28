pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
		echo 'Running build automation'
                sh 'sudo /etc/init.d/apache2 start'
            }
        }
        stage('Pre-build Test') {
            steps {
		echo 'Checking for Syntax errors'
		sh 'python -m py_compile init.py'
		echo 'Application Smoke test'
                sh 'curl -Is localhost:5000 | head -1'
            }
        }
	stage('DeployToStaging') {
            steps {
                echo 'deploy flask app'
                sh 'cp init.py /var/www/flask'
		sh 'sudo /etc/init.d/apache2 restart'
            }
        }
	stage('Post-build Test') {
            steps {
                echo 'Application Smoke test'
                sh 'curl -Is localhost | head -1'
            }
        }


    }
}

