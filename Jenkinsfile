def bucket = 'myjenkinsbucket'

pipeline {
  agent any

  environment {
        // Make use of this environment variable to build deployment logic
        ENV_NAME = "${env.BRANCH_NAME}"
    }
    tools {
        nodejs 'localnode' // define the alias of your nodejs installation here
    }

  stages {
    stage('Pre-build') {
      steps {
        echo "Install the dependencies"
        sh 'npm install'
      }
    }
    stage('Test and Build') {
      parallel {
        stage('Test') { // Comment out or delete this stage if not needed.
          steps {
            sh 'npm run test'
          }
        }
        stage('Build') {
          steps {
            echo "Create production optimized build"
            sh 'npm run build'
          }
          post {
                success {
                    echo "Deploying the code to s3 bucket - ${bucket}"
                    withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-s3-bucket']]) {
                    sh "aws s3 cp build s3://${bucket} --recursive"
                    }
                }
                failure {
                  echo "Unable to create build,please check the console output for errors and fix them"
                  cleanWs() // To clean the workspace
                }
            }
        }
      }
    }
  }
}
