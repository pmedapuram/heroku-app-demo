pipeline {
    agent any

    stages {
        stage('Init') {
            checkout scm
        }
        stage('Example Build') {
            steps {
                sh 'mvn clean install'
            }
        }
    }
}
