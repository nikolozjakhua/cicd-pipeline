pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE_MAIN = 'nodemain:v1.0'
        DOCKER_IMAGE_DEV = 'nodedev:v1.0'
    }
    
    tools {
        nodejs 'Nodejs'
    }
    
    stages {
        stage('Build') {
            steps {
                // Install dependencies
                sh 'npm install'
            }
        }
        
        stage('Test') {
            steps {
                // Run tests
                sh 'npm test'
            }
        }
        
        stage('Build Docker Images') {
            steps {
                // Build Docker images
                script {
                    if (env.BRANCH_NAME == 'main') {
                        sh 'docker build -t ${DOCKER_IMAGE_MAIN} .'
                    } else if (env.BRANCH_NAME == 'dev') {
                        sh 'docker build -t ${DOCKER_IMAGE_DEV} .'
                    }
                }
            }
        }
        
        stage('Cleanup and Deploy') {
            steps {
                // Stop and remove previously running containers
                    try {
                        sh 'docker stop $(docker ps -aq --filter "name=node*" --format="{{.Names}}")'
                    } catch (Exception e) {
                        echo "No previously deployed container: ${e.message}"
                    }

                    try {
                        sh 'docker rm $(docker ps -aq --filter "name=node*" --format="{{.Names}}")'
                    } catch (Exception e) {
                        echo "No containers to remove: ${e.message}"
                    }                
                // Run containers based on branch
                script {
                    if (env.BRANCH_NAME == 'main') {
                        sh 'docker run -d --expose 3000 -p 3000:3000 --name nodemain${env.BUILD_NUMBER} ${DOCKER_IMAGE_MAIN}'
                    } else if (env.BRANCH_NAME == 'dev') {
                        sh 'docker run -d --expose 3001 -p 3001:3000 --name nodedev${env.BUILD_NUMBER} ${DOCKER_IMAGE_DEV}'
                    }
                }
            }
        }
    }
}
