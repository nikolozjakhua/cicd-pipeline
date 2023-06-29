pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE_MAIN = 'nodemain:v1.0'
        DOCKER_IMAGE_DEV = 'nodedev:v1.0'
    }
    
    tools {
        nodejs 'nodejs-global'
    }
        
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
                sh 'docker build -t ${DOCKER_IMAGE_MAIN} .'
                sh 'docker build -t ${DOCKER_IMAGE_DEV} .'
            }
        }
        
        stage('Cleanup and Deploy') {
            steps {
                // Stop and remove previously running containers
                sh 'docker stop $(docker ps -q) || true'
                sh 'docker rm $(docker ps -aq) || true'
                
                // Run containers based on branch
                if (env.BRANCH_NAME == 'main') {
                    sh 'docker run -d --expose 3000 -p 3000:3000 ${DOCKER_IMAGE_MAIN}'
                } else if (env.BRANCH_NAME == 'dev') {
                    sh 'docker run -d --expose 3001 -p 3001:3000 ${DOCKER_IMAGE_DEV}'
                }
            }
        }
    }
}

