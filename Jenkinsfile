
pipeline {
    agent any

    options {
        disableConcurrentBuilds()
        timestamps()
    }

    triggers {
        // Check GitHub for new commits approximately every 2 minutes
        pollSCM('H/2 * * * *')
    }

    stages {
        stage('Checkout from GitHub') {
            steps {
                checkout scm
            }
        }

        stage('Validate HTML') {
            steps {
                sh '''
                    set -eu

                    test -s index.html
                    grep -qi '<html' index.html

                    echo "HTML validation passed."
                '''
            }
        }

        stage('Build Backup') {
            steps {
                sh '''
                    set -eu

                    sudo /usr/local/sbin/deploy-jenkins-demo "$WORKSPACE"
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                    set -eu

                    test -s /opt/apache-tomcat-10.1.56/webapps/jenkins-demo/index.html

                    echo "Tomcat deployment file exists."
                '''
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: Build backup and Tomcat deployment completed.'
        }

        failure {
            echo 'FAILED: Check Console Output before retrying.'
        }
    }
}
