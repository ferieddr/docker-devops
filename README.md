# DevOps with Docker 2023

## Eddie Fernberg

Submissions for the course tasks.

## Part 1

### 1.1

```shell
docker ps -a
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS                      PORTS     NAMES
b120a49e1bdf   nginx     "/docker-entrypoint.…"   55 seconds ago   Exited (0) 21 seconds ago             objective_merkle
915405b6f185   nginx     "/docker-entrypoint.…"   56 seconds ago   Exited (0) 21 seconds ago             stupefied_joliot
92e3dfe45b83   nginx     "/docker-entrypoint.…"   58 seconds ago   Up 58 seconds               80/tcp    charming_euler
```

### 1.2

```shell
docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

```shell
docker images
REPOSITORY   TAG       IMAGE ID   CREATED   SIZE
```

### 1.3

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

### 1.4

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

### 1.5

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

### 1.6

```shell
docker run -it devopsdockeruh/pull_exercise

Give me the password: basics
You found the correct password. Secret message is:
"This is the secret message"

exit
```

### 1.7

Dockerfile:

```dockerfile
FROM ubuntu:20.04
WORKDIR /usr/src/app
RUN apt-get update
RUN apt-get -y install curl
#the script with the code for loading the webpage
COPY script.sh .
CMD ./script.sh
```

### 1.8

Dockerfile:

```dockerfile
FROM devopsdockeruh/simple-web-service:alpine
CMD server
```

Command to start server:

```shell
docker run web-server
```

### 1.9

```shell
mkdir tmpdir
touch tmpdir/log.txt

docker run -v "$(pwd)/tmpdir/log.txt:/usr/src/app/text.log" devopsdockeruh/simple-web-service
```

### 1.10

```shell
#build the image (I had removed it earlier)
docker build -t sws .

docker run -p 8080:8080 sws
```

### 1.11

Dockerfile:

```dockerfile
FROM openjdk:8
EXPOSE 8080
WORKDIR /usr/src/app
COPY . .
RUN ./mvnw package
CMD ["java", "-jar", "./target/docker-example-1.1.3.jar"]
```

### 1.12

Dockerfile:

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

### 1.13

Dockerfile: 

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

Command to build and start:

```shell
docker build -t backend .
docker run -p 8080:8080 backend
```

### 1.14

Dockerfile (backend):

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

Dockerfile (frontend):

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

Commands to build and start:

```shell
docker build example-frontend/ -t frontend
docker build example-backend/ -t backend

docker run -d -p 5000:5000 frontend
docker run -d -p 8080:8080 backend
```

### 1.15

The image pushed to Docker hub can be found at [efernber/angled-name](https://hub.docker.com/repository/docker/efernber/angled-name/general).

Usage:

```shell
docker run -p 8282:8282 efernber/angled-name
```

Go to `http://localhost:8282`. The page should load.

To angle a name - add the parameter `?name=your-chosen-name` after the URL and press enter.

Voilà! The name is displayed angled.

### 1.16

Dockerfile:

```dockerfile
FROM php:7.4-cli
EXPOSE 8282
WORKDIR /usr/src/app
COPY ./src .
CMD ["php", "-S", "0.0.0.0:8282", "-t", "/usr/src/app", "/usr/src/app/index.php"]
```

1. First, I registered with fly.io using my Github account. I had already pushed my project [angled-name](https://github.com/ferieddr/angled-name) to a repo on Github.
2. Once logged in I could select my repo from Github and deploy it.
3. Fly.io starts the Web CLI. I clicked the button "deploy".
4. It loads a terminal asking questions that are answered with y/N. After entering the app name, unselecting the activations of PostgreSQl and Upstash Redis and finally selecting "deploy now", we are ready to fly.
5. Fly.io builds the image the same way on your computer and then gives you the URL where the service can be reached.

My service can be reached at Fly.io on [https://angled-name.fly.dev](https://angled-name.fly.dev/).

To angle a name - add the parameter `?name=your-chosen-name` after the URL and press enter.

Voilà! The name is displayed angled.

## Part 2

### 2.1

docker-compose.yml:

```docker
version: '3.8'

services:
  simple-web-service:
    image: devopsdockeruh/simple-web-service:ubuntu
    volumes:
      - ./tmpdir:/usr/src/app/text.log
    container_name: simple-web-service
```

### 2.2

docker-compose.yml:

```docker
version: '3.8'

services:
  simple-web-service:
    image: devopsdockeruh/simple-web-service:ubuntu
    build: .
    ports:
      - 8080:8080
    command: server
```

### 2.3

docker-compose.yml:

```docker
version: '3.8'

services:
  backend:
    build: ./backend
    ports:
      - 8080:8080
    container_name: backend

  frontend:
    build: ./frontend
    ports:
      - 5000:5000
    container_name: frontend
```

### 2.4

docker-compose.yml:

```docker
version: '3.8'

services:
  backend:
    build: ./backend
    ports:
      - 8080:8080
    environment:
      REDIS_HOST: redis
    container_name: backend

  frontend:
    build: ./frontend
    ports:
      - 5000:5000
    container_name: frontend

  redis:
    image: redis:latest
    ports:
      - 6379:6379
    command: ["redis-server", "--save", "60", "1", "--loglevel", "warning"]
```

### 2.5

Command to scale correctly:

```shell
docker-compose up --scale compute=2
```

### 2.6

docker-compose.yml:

```docker
version: '3.8'

services:
  backend:
    build: ./backend
    ports:
      - 8080:8080
    environment:
      REDIS_HOST: redis
      POSTGRES_HOST: postgres
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DATABASE: postgres
    container_name: backend
    depends_on:
      - redis
      - postgres

  frontend:
    build: ./frontend
    ports:
      - 5000:5000
    container_name: frontend
    depends_on:
      - backend

  redis:
    image: redis:latest
    ports:
      - 6379:6379
    command: ["redis-server", "--save", "60", "1", "--loglevel", "warning"]
    container_name: redis

  postgres:
    image: postgres:latest
    environment:
      POSTGRES_HOST: postgres
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DATABASE: postgres
    container_name: postgres
```

### 2.7

docker-compose.yml:

```docker
version: '3.8'

services:
  backend:
    build: ./backend
    ports:
      - 8080:8080
    environment:
      REDIS_HOST: redis
      POSTGRES_HOST: postgres
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DATABASE: postgres
    container_name: backend
    depends_on:
      - redis
      - postgres

  frontend:
    build: ./frontend
    ports:
      - 5000:5000
    container_name: frontend
    depends_on:
      - backend

  redis:
    image: redis:latest
    ports:
      - 6379:6379
    command: ["redis-server", "--save", "60", "1", "--loglevel", "warning"]
    container_name: redis

  postgres:
    image: postgres:latest
    volumes:
      - type: bind
        source: ./database
        target: /var/lib/postgresql/data
    environment:
      POSTGRES_HOST: postgres
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DATABASE: postgres
    container_name: postgres
```

### 2.8

docker-compose.yml:

```docker
version: '3.8'

services:
  backend:
    build: ./backend
    ports:
      - 8080:8080
    environment:
      REDIS_HOST: redis
      POSTGRES_HOST: postgres
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DATABASE: postgres
    container_name: backend
    depends_on:
      - redis
      - postgres
      - proxy
    networks:
      - proxy-network

  frontend:
    build: ./frontend
    ports:
      - 5000:5000
    container_name: frontend
    depends_on:
      - backend
      - proxy
    networks:
      - proxy-network

  redis:
    image: redis:latest
    ports:
      - 6379:6379
    command: ["redis-server", "--save", "60", "1", "--loglevel", "warning"]
    container_name: redis
    networks:
      - proxy-network

  postgres:
    image: postgres:latest
    volumes:
      - type: bind
        source: ./database
        target: /var/lib/postgresql/data
    environment:
      POSTGRES_HOST: postgres
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DATABASE: postgres
    container_name: postgres
    networks:
      - proxy-network

  proxy:
    image: nginx:latest
    volumes:
      - type: bind
        source: ./nginx.conf
        target: /etc/nginx/nginx.conf
    ports:
      - 80:80
    container_name: proxy
    networks:
      - proxy-network

networks:
  proxy-network:
    name: proxy-network
```

### 2.9

Changes done:

**nginx.conf:**  
Add trailing slashes to both urls in proxy_pass:

* `proxy_pass http://frontend:5000/;`
* `proxy_pass http://backend:8080/;`

**Frontend Dockerfile:**  
Change environment variable `REACT_APP_BACKEND` to: `ENV REACT_APP_BACKEND_URL=http://localhost/api/`

**Backend Dockerfile:**  
Change environment variable `REQUEST_ORIGIN` to: `ENV REQUEST_ORIGIN=http://localhost`

docker-compose.yml:

```docker
version: '3.8'

services:
  backend:
    build: ./backend
    ports:
      - 8080:8080
    environment:
      REDIS_HOST: redis
      POSTGRES_HOST: postgres
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DATABASE: postgres
    container_name: backend
    depends_on:
      - redis
      - postgres
      - proxy
    networks:
      - proxy-network

  frontend:
    build: ./frontend
    ports:
      - 5000:5000
    container_name: frontend
    depends_on:
      - backend
      - proxy
    networks:
      - proxy-network

  redis:
    image: redis:latest
    ports:
      - 6379:6379
    command: ["redis-server", "--save", "60", "1", "--loglevel", "warning"]
    container_name: redis
    networks:
      - proxy-network

  postgres:
    image: postgres:latest
    volumes:
      - type: bind
        source: ./database
        target: /var/lib/postgresql/data
    environment:
      POSTGRES_HOST: postgres
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DATABASE: postgres
    container_name: postgres
    networks:
      - proxy-network

  proxy:
    image: nginx:latest
    volumes:
      - type: bind
        source: ./nginx.conf
        target: /etc/nginx/nginx.conf
    ports:
      - 80:80
    container_name: proxy
    networks:
      - proxy-network

networks:
  proxy-network: 
    name: proxy-network 
```

### 2.10

Command for scanning ports:

```shell
docker run -it --rm --network host networkstatic/nmap localhost
```

Result from when scanning ports:

```shell
Starting Nmap 7.92 ( https://nmap.org ) at 2023-03-23 13:54 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.0000020s latency).
Other addresses for localhost (not scanned): ::1
Not shown: 998 closed tcp ports (reset)
PORT    STATE    SERVICE
80/tcp  filtered http
111/tcp open     rpcbind

Nmap done: 1 IP address (1 host up) scanned in 1.40 seconds
```

## Part 3

### 3.1

The repo with the configurations can be found at [ferieddr/express-app](https://github.com/ferieddr/express-app).

*I changed the .gitignore to include the docker-compose file and show my code to start the services.*

### 3.2

The repo with the configurations can be found at [ferieddr/angled-name-cicd](https://github.com/ferieddr/angled-name-cicd).

It uses the project from 1.16, my angled-name web application.

### 3.3

Shell script `builder.sh`:

```shell
#!/bin/sh

repo=$1
dhub=$2
# regex to get only last part of url
dir=${repo##*/}

git clone https://github.com/$repo
cd $dir
docker build -t $dhub:latest .
docker image push $dhub:latest
echo "All done. Farewell."
```

### 3.4

Dockerfile:

```docker
FROM docker:git
WORKDIR /mydir
COPY ./builder.sh ./builder.sh
RUN chmod a+x ./builder.sh
ENTRYPOINT ["./builder.sh"]
```

Shell script `builder.sh`:

```shell
#!/bin/sh

repo=$1
dhub=$2
# regex to get only last part of url
dir=${repo##*/}

git clone https://github.com/$repo
cd $dir
docker build -t $dhub:latest .
echo "$DOCKER_PWD" | docker login --username $DOCKER_USER --password-stdin
docker image push $dhub:latest
echo "All done. Farewell."
```

### 3.5

Frontend Dockerfile:

```docker
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
ENV REACT_APP_BACKEND_URL=http://localhost/api/
RUN npm run build
RUN npm install -g serve

RUN useradd -m appuser
USER appuser

CMD ["serve", "-s", "-l", "5000", "build"]
```

Backend Dockerfile:

```docker
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
ENV REQUEST_ORIGIN=http://localhost
RUN go build

RUN useradd -m appuser
USER appuser

CMD ./server
```

*My backend file is based on Ubuntu and not Alpine. These commands would I use if it would be:*

```docker
[...]

RUN adduser -D appuser
USER appuser

CMD ./server
```

### 3.6

Initial size of images:

```shell
docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED      SIZE
frontend     latest    66a1c151f80e   9 days ago   617MB
backend      latest    a6554abc5282   9 days ago   792MB
```

Reduced size of images:

```shell
docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
frontend     latest    8b21684d98cc   34 seconds ago   616MB
backend      latest    66cf06028077   5 minutes ago    663MB
```

### 3.7

Initial size of images (based on Ubuntu):

```shell
docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
frontend     latest    8b21684d98cc   34 seconds ago   616MB
backend      latest    66cf06028077   5 minutes ago    663MB
```

Reduced size of images (based on Alpine):

```shell
docker image ls
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
frontend      latest    7b963977507a   2 minutes ago   466MB
backend       latest    b1ac249d0145   5 minutes ago   645MB
```

### 3.8

Frontend Dockerfile:

```docker
FROM node:16-alpine as frontend-stage1
WORKDIR /usr/src/app
COPY . .
RUN npm install
RUN npm run build

FROM node:16-alpine
ENV REACT_APP_BACKEND_URL=http://localhost/api/
WORKDIR /usr/src/app/
COPY --from=frontend-stage1 /usr/src/app/ .
RUN npm install -g serve
CMD ["serve", "-s", "-l", "5000", "build"]
```

Size of the built image:

```shell
docker image ls
REPOSITORY    TAG       IMAGE ID       CREATED              SIZE
frontend      latest    89b06494d661   6 minutes ago        129MB
```

### 3.9

Backend Dockerfile:

```docker
FROM golang:1.16.15-alpine as backend-stage1
WORKDIR /usr/src/app
COPY . .
RUN go mod download && go mod verify
RUN go build -ldflags "-s -w"

FROM alpine:3.14
ENV REQUEST_ORIGIN=http://localhost
WORKDIR /usr/src/app
COPY --from=backend-stage1 /usr/src/app/server/ .
CMD ./server
```

Size of built image:

```shell
docker image ls
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
backend      latest    9ef698886af1   7 seconds ago   18.7MB
```

### 3.10

I chose to work with [angled-name project](https://github.com/ferieddr/angled-name). 

Initial Dockerfile:

```docker
FROM php:7.4-cli
EXPOSE 8282
WORKDIR /usr/src/app
COPY ./src .
CMD ["php", "-S", "0.0.0.0:8282", "-t", "/usr/src/app", "/usr/src/app/index.php"]
```

Size of this image when built:

```shell
docker image ls
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
angled-name   latest    4a3193a11e66   2 hours ago    474MB
```

Optimised version of the Dockerfile:

```docker
FROM alpine
WORKDIR /usr/src/app
COPY ./src .
RUN apk add --no-cache php && adduser -D userapp
USER userapp
CMD ["php", "-S", "0.0.0.0:8282", "-t", "/usr/src/app", "/usr/src/app/index.php"]
```

Size of this image when built:

```shell
docker image ls
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
angled-name   latest    60dd506831a8   6 seconds ago   18.4MB
```