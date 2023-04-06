pipeline {
    agent any
    tools { 
      maven 'Maven3'
    }

    stages {
	stage('Clean the Workspace') {
            steps {
                echo 'Clean the Workspace'
                sh "mvn clean"
            }
        }
	stage('Building the Image') {
            steps {
                echo 'Clean the Workspace'
                sh "mvn install"
            }
        }	
    }
}
