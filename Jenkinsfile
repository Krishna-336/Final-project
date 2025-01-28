pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = 'dckr_pat_PUIqhGSMqi5_XSysVglaU9HSAIc'
        DOCKER_USER = 'bala336'
        DOCKER_PASS = 'Bala@4797'
        DOCKER_IMAGE_NAME_DEV = 'bala336/dev:v1'
        DOCKER_IMAGE_NAME_PROD = 'bala336/prod:v1'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    
                    def branch = env.GIT_BRANCH ? env.GIT_BRANCH.replace("origin/", "") : sh(script: "git rev-parse --abbrev-ref HEAD", returnStdout: true).trim()

                    
                    echo "Checking out branch: ${branch}"
                    if (branch == "main") {
                        echo "Checked out 'main' branch"
                        git branch: 'main', url: 'https://github.com/Krishna-336/Final-project.git'
                    } else {
                        echo "Checked out 'dev' branch"
                        git branch: 'dev', url: 'https://github.com/Krishna-336/Final-project.git'
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image from the checked out branch"
                    sh 'chmod +x build.sh'
                    
                    sh './build.sh'
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    
                    echo "Logging into Docker Hub"
                    sh "docker login -u $DOCKER_USER -p $DOCKER_PASS"

                    
                    def branch = env.GIT_BRANCH ? env.GIT_BRANCH.replace("origin/", "") : sh(script: "git rev-parse --abbrev-ref HEAD", returnStdout: true).trim()

                    if (branch == "main") {
                        echo "Pushing Docker image to 'prod' repository"
                        
                        sh "docker tag $DOCKER_IMAGE_NAME_DEV $DOCKER_IMAGE_NAME_PROD"
                        sh "docker push $DOCKER_IMAGE_NAME_PROD"
                    } else {
                        echo "Pushing Docker image to 'dev' repository"
                        
                        sh "docker push $DOCKER_IMAGE_NAME_DEV"
                    }
                }
            }
        }

        stage('Deploy to Server') {
            steps {
                script {
                    echo "Deploying to the server"
                    sh 'chmod +x deploy.sh'
                    
                    sh './deploy.sh'
                }
            }
        }
    }

    post {
        success {
            echo 'Build and deployment succeeded!'
        }
        failure {
            echo 'Build or deployment failed!'
        }
    }
}
