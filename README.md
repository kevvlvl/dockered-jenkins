# Setup Jenkins

### create a persistent volume

docker volume create jenkins-vol

### build Jenkins

docker image build -f jenkins-Dockerfile -t jenkins:0.0.1 .

### run jenkins mounted to the volume to a detached container

$ docker container run -d --name jenkins \
                   -p 8080:8080 \
                   -p 50000:50000 \
                   --mount source=jenkins-vol,target=/var/jenkins_home \
                   -v /var/run/docker.sock:/var/run/docker.sock \
                   jenkins:0.0.1

launch jenkins on http://localhost:8080 and complete initial setup of Jenkins

    using admin account testuo:admin123

### create a sample pipeline to build and deployer an nginx container

    
