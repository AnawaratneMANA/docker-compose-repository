# Jenkins installation (Docker)

``` yml
version: "3.8"
services:
  jenkins:
    image: jenkins/jenkins:lts
    privileged: true
    user: root
    ports:
      - "8080:8080"
      - "50000:50000"
    container_name: jenkins
    volumes: 
      - jenkins-data:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
volumes: 
  jenkins-data:
```
### Running the docker-compose file
`docker-compose up -d`   

# SonarQube installation (Docker)

### VM (Host) configurations
 `vm.max_map_count=524288`   
 `fs.file-max=13107`
### To apply the changes run the following command.
 `sudo sysctl -p`  

 ### Docker Compose file
 
 ```yml
version: "1"
services:
  sonarqube:
    image: sonarqube:community
    depends_on:
      - db
    environment:
      SONAR_JDBC_URL: jdbc:postgresql://db:5432/sonar
      SONAR_JDBC_USERNAME: sonar
      SONAR_JDBC_PASSWORD: sonar
    volumes:
      - sonarqube_data:/opt/sonarqube/data
      - sonarqube_extensions:/opt/sonarqube/extensions
      - sonarqube_logs:/opt/sonarqube/logs
    ports:
      - "9000:9000"
  db:
    image: postgres:12
    environment:
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: sonar
    volumes:
      - postgresql:/var/lib/postgresql
      - postgresql_data:/var/lib/postgresql/data

volumes:
  sonarqube_data:
  sonarqube_extensions:
  sonarqube_logs:
  postgresql:
  postgresql_data:
```

### Running the docker-compose file
`docker-compose up -d`