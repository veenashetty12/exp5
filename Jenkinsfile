
pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }

    environment {
        DOCKER_CLI = 'C:\\Users\\Admin\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe'
        DOCKER_IMAGE = 'veenaaaa/jenkins-demo'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Application') {
            steps {
                echo 'Checking application files...'
                bat 'dir'
            }
        }

        stage('Test Docker') {
            steps {
                bat '"%DOCKER_CLI%" version'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                bat '"%DOCKER_CLI%" build -t %DOCKER_IMAGE%:latest .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub_credentials',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    powershell '''
                        $ErrorActionPreference = 'Stop'

                        $env:DOCKER_PASS | & $env:DOCKER_CLI login --username $env:DOCKER_USER --password-stdin
                        if ($LASTEXITCODE -ne 0) { exit $LASTEXITCODE }

                        & $env:DOCKER_CLI push "$($env:DOCKER_IMAGE):latest"
                        if ($LASTEXITCODE -ne 0) { exit $LASTEXITCODE }

                        & $env:DOCKER_CLI logout
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }

        failure {
            echo 'Pipeline execution failed!'
        }
    }
}
