def _nginxImg
def _nginxFileExists
def _dockerFileDir

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
            steps{
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
            agent {
                dockerfile {
                    filename "${params.NGINX_DOCKERFILE}"
                    dir "$_dockerFileDir"
                }
            }
            steps {
                echo 'Built container'
            }
        }
    }
}
