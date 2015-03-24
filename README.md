# docker-play
Playing with docker, continuous integration and automatic deploy.

Original article [Node With Docker - Continuous Integration and Delivery](http://mherman.org/blog/2015/03/06/node-with-docker-continuous-integration-and-delivery/#.VQ9YhZPF-6B).

# Install

```bash
brew update
brew install docker
brew install boot2docker
brew install docker-composer
$boot2docker init
$boot2docker up
Waiting for VM and Docker daemon to start...
.........................ooooooooooooooooooooooooooooo
Started.
Writing /Users/jrodriguez/.boot2docker/certs/boot2docker-vm/ca.pem
Writing /Users/jrodriguez/.boot2docker/certs/boot2docker-vm/cert.pem
Writing /Users/jrodriguez/.boot2docker/certs/boot2docker-vm/key.pem

To connect the Docker client to the Docker daemon, please set:
    export DOCKER_TLS_VERIFY=1
    export DOCKER_HOST=tcp://192.168.59.103:2376
    export DOCKER_CERT_PATH=/Users/jrodriguez/.boot2docker/certs/boot2docker-vm
```

$exports (do exports)

# Example project

## Dockerfiles

### app/Dockerfile

```javascript
FROM dockerfile/nodejs

RUN mkdir /src

RUN npm install nodemon -g

WORKDIR /src
ADD . /src
RUN npm install

EXPOSE 3000

CMD npm start
```

### redis/Dockerfile

## Docker-composer file (docker-example.yml)
```javascript
web:
  build: ./app
  volumes:
    - "app:/src/app"
  ports:
    - "80:3000"
  links:
   - redis
redis:
    build: ./redis
    ports:
        - "6379:6379"
```

```bash
$docker-compose up (prepare the containers, get and start all the dependencies defined in Dockerfiles)
```

# Continuous Integration

## Docker Hub
1. Signup using your Github credentials.
2. Set up a new automated build:
..1 Add a clone of this, replicated on your github account.
..2 “Dockerfile Location” - change that to “/app”. 

## CircleCI
1. Signup using Github credentials.
2. Create a new project using the Github repo you created.
3. Use the configuration file (circle.yml).
```javascript
machine:
  services:
    - docker

dependencies:
  override:
    - curl -L https://github.com/docker/compose/releases/download/1.1.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose chmod +x /usr/local/bin/docker-compose

test:
  override:
    - docker-compose run -d --no-deps web
    - cd app; mocha
```

