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
  stages {
    stage('Init') {
        steps {
            checkout scm
        }
    }
    stage('Run maven') {
        steps {
            container('maven') {
                sh """mvn --batch-mode --fail-at-end --strict-checksums --update-snapshots \
                    -Dorg.slf4j.simpleLogger.log.org.apache.maven.cli.transfer.Slf4jMavenTransferListener=warn \
                    clean install
                    """
            }
        }
    }

    stage('Slug Add Artifacts') {
        steps{
            echo 'Hello'
        }
    }
  }
}
