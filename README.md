# Jenkins CI pipeline
Notes for myself regarding my docker setup.

Dockerfile for Jenkins:
```
FROM jenkins/jenkins:2.414.3-jdk17
USER root
RUN apt-get update && apt-get install -y lsb-release
RUN curl -fsSLo /usr/share/keyrings/docker-archive-keyring.asc \
https://download.docker.com/linux/debian/gpg
RUN echo "deb [arch=$(dpkg --print-architecture) \
signed-by=/usr/share/keyrings/docker-archive-keyring.asc] \
https://download.docker.com/linux/debian \
$(lsb_release -cs) stable" > /etc/apt/sources.list.d/docker.list
RUN apt-get update && apt-get install -y docker-ce-cli
USER jenkins
RUN jenkins-plugin-cli --plugins "blueocean docker-workflow"
```
Run Jenkins while exposing docker.sock to enable creating sibling containers:
```
docker run -v /var/run/docker.sock:/var/run/docker.sock --name jenkins-blueocean --detach --network jenkins --publish 8020:8080 --publish 50000:50000 --volume jenkins-data:/var/jenkins_home --volume jenkins-docker-certs:/certs/client:ro myjenkins-blueocean
```
Check the ID of the docker users group:
```
ls -l /var/run/docker.sock
```
Open terminal of the docker container with Jenkins:
```
docker exec -it -u root jenkins-bluestacks bash
```
Add the docker group to the jenkins container:
```
groupadd -g <ID> docker
```
Add jenkins user to the docker group:
```
gpasswd -a jenkins docker
```

Sonar docker-compose.yml (is database recognised?):
```
services:
    sonarqube:
        container_name: sonarqube
        image: sonarqube
        depends_on:
            - sonarqube-database
        environment:
            - SONARQUBE_JDBC_USERNAME=sonarqube
            - SONARQUBE_JDBC_PASSWORD=sonarpass
            - SONARQUBE_JDBC_URL=jdbc:postgresql://sonarqube-database:5432/sonarqube
        volumes:
            - sonarqube_conf:/opt/sonarqube/conf
            - sonarqube_data:/opt/sonarqube/data
            - sonarqube_extensions:/opt/sonarqube/extensions
            - sonarqube_bundled-plugins:/opt/sonarqube/lib/bundled-plugins
        ports:
            - 9000:9000
        networks:
            - jenkins

    sonarqube-database:
        container_name: sonarqube-database
        image: postgres:12
        environment:
            - POSTGRES_DB=sonarqube
            - POSTGRES_USER=sonarqube
            - POSTGRES_PASSWORD=sonarpass
        volumes:
            - sonarqube_database:/var/lib/postgresql
            - sonarqube_database_data:/var/lib/postgresql/data
        ports:
            - 5432:5432
        networks:
            - jenkins

volumes:
    sonarqube_database_data:
    sonarqube_bundled-plugins:
    sonarqube_conf:
    sonarqube_data:
    sonarqube_database:
    sonarqube_extensions:

networks:
    jenkins:
        external: true
```

For Tomcat container the following image can be used:\
https://github.com/docker-library/tomcat/blob/70ec135dc89b4928b83a31fbe2ceaea6c19de8fb/9.0/jdk17/temurin-jammy/Dockerfile
```
docker build -t tomcat-jdk17 .
docker run --detach --network jenkins --name tomcat --publish 8030:8080 tomcat-jdk17
```
After deployment the app should be accessible in http://localhost:8030/module-main/swagger-ui/