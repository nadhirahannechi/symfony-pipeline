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
           


        stage('Continuous Deployment - Production') {
            /*when { expression { return env.BRANCH_NAME in ['master'] } }*/
            steps {
                build job: "${FOLDER_NAME}/Production Deploy", wait: false
            }
        }
    }

   /* post {
        always { cleanWs deleteDirs: true, patterns: [
            [pattern: '**/.cache/**', type: 'EXCLUDE'],
            [pattern: 'node_modules', type: 'EXCLUDE']
        ] }
        fixed { slackSend(color: 'good', message: "Fixed - ${FOLDER_NAME} - ${env.BUILD_DISPLAY_NAME} (<${env.BUILD_URL}|Open>)\n${env.BRANCH_NAME}")}
        failure { slackSend(color: 'danger', message: "Failed - ${FOLDER_NAME} - ${env.BUILD_DISPLAY_NAME} (<${env.BUILD_URL}|Open>)\n${env.BRANCH_NAME}") }*/
    }
}
