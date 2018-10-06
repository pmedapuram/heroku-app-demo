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
    image: gcr.io/core-1-190918/heroku-app-demo:debug
    command:
    - cat
    tty: true
"""
    }
  }
  options { timestamps() }

  stages {
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
      environment {
          HEROKU_KEY = credentials('pavan-heroku-token')
          GITHUB = credentials('pavan-github-token')
      }
        steps{
            container('dev-heroku') {
              script {


                sh 'entrypoint slug_add_artifacts target/java-getting-started-1.0.jar'
                def deployCloneUrl = "https://github.com/spindemo/deploy-manifest.git"
                    sh "git clone ${deployCloneUrl}"
                    def appNames = []
                    appNames << sh([returnStdout: true, script: 'jq \'.app_name\' deploy-manifest/heroku-app-demo/staging/oregon/heroku.json']).trim()
                    appNames << sh([returnStdout: true, script: 'jq \'.app_name\' deploy-manifest/heroku-app-demo/production/oregon/heroku.json']).trim()
                    echo "The appNames are ${appNames[0]} and ${appNames[1]}"
                    sh "entrypoint slug_create --app-names pmedapuram-debug-jdk --token ${env.HEROKU_KEY} --deploy-dir deploy-manifest/heroku-app-demo/staging/oregon"
                    //sh "entrypoint slug_create --app-names ${appNames[1]} --token ${env.HEROKU_KEY} --deploy-dir deploy-manifest/heroku-app-demo/production/oregon"

                    def netrcPath='~/.netrc'

                    sh """
                          echo '\nmachine github.com' >> ${netrcPath}
                          echo 'login ${env.GITHUB_USR}' >> ${netrcPath}
                          echo 'password ${env.GITHUB_PSW}' >> ${netrcPath}
                    """
                    sh "git config --global user.name ${env.GITHUB_USR}"
                    sh "git config --global user.email pmedapuram@salesforce.com"


                    echo "Uploading the slug for staging-oregon"
                        def pathToJson = 'deploy-manifest/heroku-app-demo/staging/oregon/heroku.json'
                        def tarball = "${env.WORKSPACE}/slug.tgz"
                        def slugUploadUrl = sh([returnStdout: true, script: "jq --raw-output '.upload_url' ${pathToJson}"]).trim()
                        sh """
                          curl --fail \
                          --connect-timeout 5 \
                          --max-time 60 \
                          --retry 3 \
                          --retry-delay 0 \
                          --retry-max-time 300 \
                          -X PUT \
                          -H "Content-Type:" \
                          --data-binary @${tarball} \
                          -n "${slugUploadUrl}"
                          """
                }


            }
        }
    }
  }

}
