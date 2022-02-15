# TP1

## Database

build
```
docker build -t db .
```

## Récupération de l'image adminer
```
docker pull adminer
```

## Création du network
```
docker network create mynetwork
```

## Execution des containers

`docker run --network mynetwork --name db -d -e POSTGRES_PASSWORD="pwd" db`

̀`docker run --network mynetwork -p 8080:8080 -d --name db_view adminer`

## persistance
```
docker run --network mynetwork --name db -d -e POSTGRES_PASSWORD="pwd" -v /tmp/data:/var/lib/postgresql/data db
```

## Dockerfile

```

FROM postgres:11.6-alpine

ENV POSTGRES_DB=db \
    POSTGRES_USER=usr 

COPY sql/ /docker-entrypoint-initdb.d
```


## 1-2

```
# Build
FROM maven:3.6.3-jdk-11 AS myapp-build
ENV MYAPP_HOME /fs03/share/users/dorian.duveau/home/TP01/Backend_api/simple-api
WORKDIR $MYAPP_HOME
COPY /simple-api/pom.xml .
COPY /simple-api/src ./src
RUN mvn package -DskipTests
# Run
FROM openjdk:11-jre
ENV MYAPP_HOME /fs03/share/users/dorian.duveau/home/TP01/Backend_api/simple-api
WORKDIR $MYAPP_HOME
COPY --from=myapp-build $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp.jar
ENTRYPOINT java -jar myapp.jar
```
Why do we need a multistage build? : afin de ne pas gardez des outils inutiles au run. Dans un premier temps on compile avec un jdk puis on le run

docker compose most important commands: 
`docker-compose up --build`

```
version: '3.3'
services:
  backend:
    container_name: backend
    build: ./simple-api-main
    networks:
      - backend
      - frontend
    depends_on:
      - db

  db:
    container_name: db
    build: ./DB
    networks:
      - backend
    volumes: 
      - /tmp/data:/var/lib/postgresql/data

  httpd:
    container_name: httpd
    build: ./httpd
    ports:
      - 80:80
    networks:
      - frontend
    depends_on:
      - backend

networks:
  backend:
  frontend:
```

#TP2

what are testcontainers? Une librairie java supportant JUnit et une BD dockerisable

#TP3

```
all:
  vars:
    ansible_user: centos
    ansible_ssh_private_key_file: ~/.ssh/id_rsa_devops
  children:
    prod:
      hosts: dorian.duveau.takima.cloud
```

`ansible all -i inventories/setup.yml -m ping`

##Playbook

```
- hosts: all
  gather_facts: false
  become: yes
  tasks:
    - name: Test connection
      ping:
```
`ansible-playbook -i inventories/setup.yml playbook.yml`
