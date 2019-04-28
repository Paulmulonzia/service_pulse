pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
		echo 'Running build automation'
                sh 'python app.py'
            }
        }
        stage('Test') {
            steps {
                sh 'curl -Is localhost:5000 | head -1'
            }
        }
    }
}

