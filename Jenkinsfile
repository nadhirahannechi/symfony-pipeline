#!groovy

FOLDER_NAME = env.JOB_NAME.split('/')[0]

pipeline {
    agent {
        docker {
                    image 'runroom/php8.1-cli'
                    args '-v $HOME/composer:/home/jenkins/.composer:z'
                    reuseNode true
                }
    }
    environment {
        APP_ENV = 'test'

    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
        disableConcurrentBuilds()
    }

    stages {
        stage('Install packages') {
            steps {
                // Install
                sh 'composer install --no-progress --no-interaction'
            }
          }
         stage('Coverage') {
            steps {
                // Coverage
                sh 'vendor/bin/phpunit --log-junit coverage/unitreport.xml --coverage-html coverage'
            }
          }
         stage('Unit Test') {
            steps {
                xunit([PHPUnit(
                    deleteOutputFiles: false,
                    failIfNotNew: false,
                    pattern: 'coverage/unitreport.xml',
                    skipNoTestFiles: true,
                    stopProcessingIfError: false
                )])
            }
          }
         stage('Report') {
            steps {
                 publishHTML(target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: false,
                    keepAll: true,
                    reportDir: 'coverage',
                    reportFiles: 'index.html',
                    reportName: 'Coverage Report'
                ])
            }
          }
        stage('Deploy Stage') {
      steps { 
      sh 'ls -a'
      timeout(time: 500, unit: 'SECONDS') {
          sh 'ls' 
          pushToCloudFoundry(
              target: 'https://api.cf.us10.hana.ondemand.com/',
               organization: '2b1f4fe8trial',
               cloudSpace: 'dev',
                credentialsId: 'nadhira',
               )
        }
      }
    }
           


       
    }
}
