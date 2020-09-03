def _nginxImg
def _nginxFileExists

pipeline {

    agent any
    options {
        timestamps()
    }
    
    parameters {
        string(name: 'GIT_REPO', defaultValue: 'https://github.com/kevvlvl/dockered-jenkins.git', description: 'Public Git repo to clone', trim: true)
        string(name: 'NGINX_DOCKERFILE', defaultValue: 'nginx-Dockerfile', description: 'Name of Nginx Dockerfile', trim: true)
    }
    
    stages {
        stage('Git clone') {
            steps {
                git "${params.GIT_REPO}"
            }
        }
        
        stage('LS Git repo') {
            steps{
                script {
                    sh "ls -lah"
                    _nginxFileExists = fileExists "./${params.NGINX_DOCKERFILE}"
                    echo "NGINX file exists? $_nginxFileExists"
                }
            }
        }
        
        stage('Build NGINX docker container') {
            when { expression { _nginxFileExists == true } }
            steps{
                script {
                    _nginxImg = docker.build('nginx:0.0.1', "-f ${params.NGINX_DOCKERFILE} .")
                }
            }
        }
    }
}
