pipeline {
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
  - name: dev-heroku
    image: gcr.io/core-1-190918/heroku-app-demo:latest
    command:
    - cat
    tty: true
"""
    }
  }
  options { timestamps() }

  stages {
    stage('Init') {
        steps {
            checkout scm
        }
    }
    stage('Build and Test') {
        steps {
            container('maven') {
                sh """mvn --batch-mode --fail-at-end --strict-checksums --update-snapshots \
                    -Dorg.slf4j.simpleLogger.log.org.apache.maven.cli.transfer.Slf4jMavenTransferListener=warn \
                    clean install
                    """
            }
        }
    }

    stage('Add artifacts to slug') {
        steps{
            container('dev-heroku') {
                sh 'entrypoint slug_add_artifacts target/*.jar'
            }
        }
    }

    stage('Slug create') {
        environment {
            HEROKU_KEY = credentials('pavan-heroku-token')
        }
        steps{
            container('dev-heroku') {
                def appNames = []
                appNames = sh('echo hello')
                //sh 'entrypoint slug_create target/*.jar'
            }
        }
    }

  }
}
