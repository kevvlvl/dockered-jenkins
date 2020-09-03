pipeline { 
    
    agent any
    
    stages {
        stage('Docker Verify') {
            steps {
                sh 'docker version'
                sh 'docker container ls'
            }
        }
        
        stage('Docker image') {
            agent {
                docker {
                    image 'nginx:1.19.2-alpine'
                }
            }
            
            steps {
                sh 'ls -a'
            }
        }
        
        stage('Docker build') {
        
            agent any
            
            steps {
                sh 'docker build -t nginx:0.0.1 .'
            }
        }
    }
}
