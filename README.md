# DevOps with Docker 2023

## Eddie Fernberg

Submissions for the course tasks.

## 1.1

```shell
docker ps -a
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS                      PORTS     NAMES
b120a49e1bdf   nginx     "/docker-entrypoint.…"   55 seconds ago   Exited (0) 21 seconds ago             objective_merkle
915405b6f185   nginx     "/docker-entrypoint.…"   56 seconds ago   Exited (0) 21 seconds ago             stupefied_joliot
92e3dfe45b83   nginx     "/docker-entrypoint.…"   58 seconds ago   Up 58 seconds               80/tcp    charming_euler
```

## 1.2

```shell
docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

```shell
docker images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
```

## 1.3

```shell¨
Secret message is: 'You can find the source code here: https://github.com/docker-hy'
2023-03-15 14:53:15 +0000 UTC
2023-03-15 14:53:17 +0000 UTC
2023-03-15 14:53:19 +0000 UTC
2023-03-15 14:53:21 +0000 UTC
2023-03-15 14:53:23 +0000 UTC
Secret message is: 'You can find the source code here: https://github.com/docker-hy'
2023-03-15 14:53:25 +0000 UTC
2023-03-15 14:53:27 +0000 UTC
2023-03-15 14:53:29 +0000 UTC
2023-03-15 14:53:31 +0000 UTC
2023-03-15 14:53:33 +0000 UTC
```

## 1.4

In terminal 1:

```shell
docker run -it --name readweb ubuntu sh -c 'while true; do echo "Input website:"; read website; echo "Searching.."; sleep 1; curl http://$website; done'
```

In terminal 2:

```shell
docker exec -it readweb bash
apt-get update
apt-get -y install curl
```

In terminal 1:

```shell
helsinki.fi
```
Press enter and voilà!

## 1.5

```shell
docker pull mdevopsdockeruh/simple-web-service:ubuntu
docker pull devopsdockeruh/simple-web-service:alpine
```

```shell
docker image ls
REPOSITORY  TAG       IMAGE ID       CREATED       SIZE
devopsdockeruh/simple-web-service   ubuntu    4e3362e907d5   2 years ago   83MB
devopsdockeruh/simple-web-service   alpine    fd312adc88e0   2 years ago   15.7MB
```

```shell
docker run -d -it --name sws-alpine devopsdockeruh/simple-web-service:alpine

docker exec -it sws-alpine sh
tail -f ./text.log
```

```shell
Secret message is: 'You can find the source code here: https://github.com/docker-hy'
2023-03-16 11:05:58 +0000 UTC
2023-03-16 11:06:00 +0000 UTC
2023-03-16 11:06:02 +0000 UTC
2023-03-16 11:06:04 +0000 UTC
2023-03-16 11:06:06 +0000 UTC
```

## 1.6

```shell
docker run -it devopsdockeruh/pull_exercise

Give me the password: basics
You found the correct password. Secret message is:
"This is the secret message"

exit
```

## 1.7

Dockerfile

```dockerfile
FROM ubuntu:20.04
WORKDIR /usr/src/app
RUN apt-get update
RUN apt-get -y install curl
#the script with the code for loading the webpage
COPY script.sh .
CMD ./script.sh
```

## 1.8

Dockerfile

```dockerfile
FROM devopsdockeruh/simple-web-service:alpine
CMD server
```

Command to start server

```shell
docker run web-server
```

## 1.9

```shell
mkdir tmpdir
touch tmpdir/log.txt

docker run -v "$(pwd)/tmpdir/log.txt:/usr/src/app/text.log" devopsdockeruh/simple-web-service
```

## 1.10

```shell
#build the image (I had removed it earlier)
docker build . -t sws 

docker run -p 8080:8080 sws
```

## 1.11

Dockerfile 

```dockerfile
FROM openjdk:8
EXPOSE 8080
WORKDIR /usr/src/app
COPY . .
RUN ./mvnw package
CMD ["java", "-jar", "./target/docker-example-1.1.3.jar"]
```

## 1.12

Dockerfile 

```dockerfile
FROM ubuntu:20.04
EXPOSE 5000
WORKDIR /usr/src/app
COPY . .
RUN apt-get update
RUN apt-get -y install curl
RUN curl -sL https://deb.nodesource.com/setup_16.x | bash
RUN apt-get update
RUN apt install -y nodejs
RUN npm install
RUN npm run build
RUN npm install -g serve
CMD ["serve", "-s", "-l", "5000", "build"]
```

## 1.13

Dockerfile 

```dockerfile
FROM ubuntu:20.04
EXPOSE 8080
RUN apt-get update
RUN apt-get -y install curl
RUN curl -sL https://go.dev/dl/go1.16.15.linux-amd64.tar.gz --output go1.16.15.linux-amd64.tar.gz
RUN rm -rf /usr/local/go
RUN tar -C /usr/local -xzf go1.16.15.linux-amd64.tar.gz
ENV PATH=$PATH:/usr/local/go/bin
RUN go version
WORKDIR /usr/src/app
COPY . .
RUN go build
RUN go test
CMD ./server
```

```shell
docker build . -t backend
docker run -p 8080:8080 backend
```

## 1.14

Dockerfile (backend)

```dockerfile
FROM ubuntu:20.04
EXPOSE 8080
RUN apt-get update
RUN apt-get -y install curl
RUN curl -sL https://go.dev/dl/go1.16.15.linux-amd64.tar.gz --output go1.16.15.linux-amd64.tar.gz
RUN rm -rf /usr/local/go
RUN tar -C /usr/local -xzf go1.16.15.linux-amd64.tar.gz
ENV PATH=$PATH:/usr/local/go/bin
RUN go version
WORKDIR /usr/src/app
COPY . .
ENV REQUEST_ORIGIN=http://localhost:5000
RUN go build
CMD ./server
```

Dockerfile (frontend)

```dockerfile
FROM ubuntu:20.04
EXPOSE 5000
WORKDIR /usr/src/app
COPY . .
RUN apt-get update
RUN apt-get -y install curl
RUN curl -sL https://deb.nodesource.com/setup_16.x | bash
RUN apt-get update
RUN apt install -y nodejs
RUN node -v && npm -v
RUN npm install
ENV PORT=5000
ENV REACT_APP_BACKEND_URL=http://localhost:8080/
RUN npm run build
RUN npm install -g serve
CMD ["serve", "-s", "-l", "5000", "build"]
```

```shell
docker build example-frontend/ -t frontend
docker build example-backend/ -t backend

docker run -d -p 5000:5000 frontend
docker run -d -p 8080:8080 backend
```