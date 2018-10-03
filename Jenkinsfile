pipeline {
    //agent any
    agent {
    kubernetes {
      label 'strata-jenkins-slave'
      defaultContainer 'jnlp'
      yaml 'BuildContainerConfig.yaml'
    }
  }

    stages {
        stage('Init') {
            steps{
                checkout scm
            }
        }
        stage('Example Build') {
            steps {
                containers('maven') {
                    sh 'mvn clean install'
                }
            }
        }
    }
}
