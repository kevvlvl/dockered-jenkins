# Setup Jenkins

The following project deploys a containerized Jenkins which uses the host Docker engine to build and deploy containers external to the Jenkins container.
The idea being that you can containerize Jenkins and implement orchestration with external SCM sources and systems, and deploy containers and/or artifacts outside of Jenkins.

### create a persistent volume

docker volume create jenkins-vol
docker volume create nexus-vol

### build Nexus

$ docker image build -f nexus-Dockerfile -t nexus:0.0.1 .

### run Nexus

$ docker container run -d --name nexus \
                 -p 8081:8081 \
                 -p 8083:8083 \
                 -v nexus-vol:/nexus-data \
                 nexus:0.0.1
                 
Launch nexus on http://localhost:8081. Click sign-in and follow procedure to recuperate password form the container's file system. Then, I changed the password for the sake of this exercise (but please keep it complex and securely stored!). Following the password change, create a Docker hosted private repository exposed on HTTP port 8083 (notice how we're exposing 8083 in the above docker container run statement).

    using admin account admin:admin123

### build Jenkins

$ docker image build -f jenkins-Dockerfile -t jenkins:0.0.1 .

### run jenkins mounted to the volume to a detached container

$ docker container run -d --name jenkins \
                   -p 8080:8080 \
                   -p 50000:50000 \
                   -v jenkins-vol:/var/jenkins_home \
                   -v /var/run/docker.sock:/var/run/docker.sock \
                   jenkins:0.0.1

launch jenkins on http://localhost:8080 and complete initial setup of Jenkins

    using admin account testuo:admin123

### create a sample pipeline to build and deployer an nginx container

See build-dockerimage.Jenkinsfile as the example.
