pipeline {
    //agent any
    agent {
        kubernetes {
            label 'heroku-app-ci'
            defaultContainer 'jnlp'
            yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    some-label: some-label-value
  spec:
    containers:
    - name: maven
      image: maven:alpine
      command:
      - cat
      tty: true
    - name: heroku-build
      image: gcr.io/core-1-190918/heroku-app-demo
      command:
      - cat
      tty: true
"""
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
                container('maven') {
                    sh 'mvn clean install'
                }
            }
        }
    }
}
