pipeline {
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME = 'sonar-scanner'
        APP_NAME = "reddit-clone-app"
        RELEASE = "1.0.0"
        DOCKER_USER = "02271589"
        DOCKER_PASS = "dockerhub"
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWS()
            }
        }

        stage('Checkout from git') {
            steps {
                git branch: 'main', url: 'https://github.com/SoftwareDevDeveloper/reddit-app-CI'
            }
        }

        //Perform code inpection and also execute static code analysis
        stage('Sonarqube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube-Server') {
                    sh '''
                    $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Reddit-Clone-CI \
                    -Dsonar.projectKey=Reddit-Clone-CI
                    '''
                }
            }
        }
        
        //This check if the code meets the expected level of quality or standard
        stage('Quality Gate') {
            steps {
                waitForQualityGate abortPipeline: false, credentialsId: 'SonarQube-Token'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }

        // This will scan the code for bugs and vulnerabilities
        stage('Trivy FS Scan') {
            steps {
                sh "trivyfs . > trivyfs.txt"
            }
        }
    }
}