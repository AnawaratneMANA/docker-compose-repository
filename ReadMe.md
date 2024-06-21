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

# Guacamole (Docker)

### Prerequisite
1. LXC container with ubuntu / docker installed
2. [Portainer](https://docs.portainer.io/start/install-ce/server/docker/linux) installed for docker management

### Installation guide
1. Run the docker-compose.yml or run it as a stack in portainer.
2. Bash into the MySQL database for user and table configuration.

3. Log into the SQL terminal `mysql -u root -p`
4. Execute following commands in order

>CREATE DATABASE guacamole_db;

>CREATE USER 'guacamole_user'@'%' IDENTIFIED BY 'pass';

>GRANT SELECT,INSERT,UPDATE,DELETE ON guacamole_db.* TO 'guacamole_user'@'%';

>FLUSH PRIVILEGES;

>SELECT USER FROM mysql.user;`

5. On the Host machine or the VM (Via Proxmox Shell if using Proxmox) execute the following commands to generate the SQL tables and copy the stricts over to the container.

>docker run --rm guacamole/guacamole /opt/guacamole/bin/initdb.sh --mysql > initdb.sql

>sudo docker cp ./initdb.sql guac-sql:/initdb.sql

6. Again from inside the MySQL database server, Run the following commands.

>USE guacamole_db;

>source ./initdb.sql


