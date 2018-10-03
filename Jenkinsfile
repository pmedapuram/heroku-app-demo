pipeline {
    agent any

    stages {
        stage('Init') {
            steps{
                checkout scm
            }
        }
        stage('Example Build') {
            steps {
                sh 'mvn clean install'
            }
        }
    }
}
