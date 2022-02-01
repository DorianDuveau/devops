# Build

docker build -t db .

# Récupération de l'image adminer

docker pull adminer

# Création du network

docker network create mynetwork

# Execution des containers

`docker run --network mynetwork --name db -d -e POSTGRES_PASSWORD="pwd" db`

̀`docker run --network mynetwork -p 8080:8080 -d --name db_view adminer`

# persistance

docker run --network mynetwork --name db -d -e POSTGRES_PASSWORD="pwd" -v /tmp/data:/var/lib/postgresql/data db

# Dockerfile

```

FROM postgres:11.6-alpine

ENV POSTGRES_DB=db \
    POSTGRES_USER=usr 

COPY sql/ /docker-entrypoint-initdb.d
```


# 1-2

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

