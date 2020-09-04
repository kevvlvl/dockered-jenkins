def _nginxFileExists
def _dockerFileDir
def _nginxContainerRunning
def _NGINX_IMAGE = 'nginx:0.0.1'
def _NGINX_CONTAINER = 'nginx'

pipeline {

    agent any
    
    options {
        timestamps()
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }
    
    parameters {
        string(name: 'GIT_REPO', defaultValue: 'https://github.com/kevvlvl/dockered-jenkins.git', description: 'Public Git repo to clone', trim: true)
        string(name: 'NGINX_DOCKERFILE', defaultValue: 'nginx-Dockerfile', description: 'Name of Nginx Dockerfile', trim: true)
    }
    
    stages {
    
        stage('Cleanup dir') {
            steps {
                script {
                    deleteDir()
                }
            }
        }
    
        stage('Git clone') {
            steps {
                git "${params.GIT_REPO}"
            }
        }
        
        stage('LS Git repo') {
            steps {
                script {
                    sh "pwd; ls -lah"
                    _nginxFileExists = fileExists "./${params.NGINX_DOCKERFILE}"
                    echo "NGINX file exists? $_nginxFileExists"
                    _dockerFileDir = sh (
                        script: 'pwd',
                        returnStdout: true
                    ).trim()
                }
            }
        }
        
        stage('Build NGINX docker container') {
            when { expression { _nginxFileExists == true } }
            steps {
                script {
                    sh "docker image build -f ${params.NGINX_DOCKERFILE} -t ${_NGINX_IMAGE} ${_dockerFileDir}"
                }
            }
            
        }
        
        stage('Deploy NGINX container') {
            steps {
                script {
                    sh "docker container run -d --name ${_NGINX_CONTAINER} -p 8010:80 ${_NGINX_IMAGE}"
                }
            }
        }
        
        stage('Validate NGINX Container') {
            steps {
                script {
                    _nginxContainerRunning = sh (
                        script: "docker container ls | grep ${_NGINX_CONTAINER} | wc -l",
                        returnStdout: true
                    ).trim()
                    
                    echo "Number of containers found for container name ${_NGINX_CONTAINER}: ${_nginxContainerRunning}"
                    
                    if(_nginxContainerRunning != '1') {
                        currentBuild.result = 'FAILURE'
                    }
                }
            }
        }
    }
}
