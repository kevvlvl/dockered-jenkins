def _nginxFileExists
def _dockerFileDir
def _NGINX_IMAGE = 'nginx:0.0.1'
def _NGINX_CONTAINER = 'nginx'
def _NEXUS_ADDRESS = 'localhost:8083'
def _NEXUS_REPO = 'docker-onprem'
def _NEXUS_IMAGE_TAG = 'localhost:8083/repository/docker-onprem/nginx:0.0.1'

pipeline {

    agent any
    
    options {
        timestamps()
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }
    
    parameters {
        string(name: 'GIT_REPO', defaultValue: 'https://github.com/kevvlvl/dockered-jenkins.git', description: 'Public Git repo to clone', trim: true)
        string(name: 'NGINX_DOCKERFILE', defaultValue: 'nginx-Dockerfile', description: 'Name of Nginx Dockerfile', trim: true)
        string(name: 'NGINX_PORT', defaultValue: '8010', description: 'Nginx port', trim: true)
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
                echo "Build the nginx docker container"
                script {
                    sh "docker image build -f ${params.NGINX_DOCKERFILE} -t ${_NGINX_IMAGE} ${_dockerFileDir}"
                    sh "docker tag ${_NGINX_IMAGE} ${_NEXUS_IMAGE_TAG}"
                }
            }
            
            post {
                success {
                    echo "Container deployed"
                }
                failure {
                    echo "Delete docker image"
                    script {
                        sh "docker image rm ${_NGINX_IMAGE}"
                    }
                }
            }   
        }
        
        stage('Push NGINX image to Nexus') {
            steps {
                echo "Push image to Nexus image registry"
                script {
                    sh "docker login -u admin -p admin123 ${_NEXUS_ADDRESS}"
                    sh "docker push ${_NEXUS_IMAGE_TAG}"
                    sh "docker logout ${_NEXUS_ADDRESS}"
                }
            }
            
            post {
                success {
                    echo "Image pushed to Nexus"
                }
                failure {
                    echo "FAILURE to push. Logout from Nexus"
                    script {
                        sh "docker logout ${_NEXUS_ADDRESS}"
                    }
                }
            }   
        }
        
        stage('Deploy NGINX container') {
            steps {
                echo "Run the nginx docker container"
                script {
                    sh "docker container run -d --name ${_NGINX_CONTAINER} -p ${params.NGINX_PORT}:80 ${_NGINX_IMAGE}"
                }
            }
            
            post {
                success {
                    echo "Container deployed"
                }
                failure {
                    echo "Stop and delete the docker container"
                    script {
                        sh "docker container stop ${_NGINX_CONTAINER}"
                        sh "docker container rm ${_NGINX_CONTAINER}"
                    }
                }
            }
        }
        
        stage('Validate NGINX Container') {
            steps {
                script {
                    def _nginxContainerRunning = sh (
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
        
        stage('Cleanup') {
            steps {
                echo "Cleanup workspace"
                deleteDir()
            }
        }
    }
}
