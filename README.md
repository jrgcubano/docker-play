# docker-play
Playing with docker, continuous integration and automatic deploy.

Original article [Node With Docker - Continuous Integration and Delivery](http://mherman.org/blog/2015/03/06/node-with-docker-continuous-integration-and-delivery/#.VQ9YhZPF-6B).

# Install

```bash
brew install docker
brew install boot2docker
brew install docker-composer
boot2docker init
boot2docker up
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

## Clone or copy this files to a fresh repository
```bash
├── app
│   ├── Dockerfile
│   ├── index.js
│   ├── package.json
│   └── test
│       └── test.js
└── redis
    └── Dockerfile
```

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

## Docker Hub (1)
1. Signup using your Github credentials.
2. Set up a new automated build:
..1 Add a clone of this, replicated on your github account.
..2 “Dockerfile Location” - change that to “/app”. 

## CircleCI (1)
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

## Docker Hub (2)
We want:
1. CircleCI to run tests against the master branch then after they pass. 
2. A new build should trigger on Docker Hub.

Steps:
Open your repository on Docker Hub, and make the following updates:
1. Under Settings click Automated Build.
2. Uncheck the Active box: “When active we will build when new pushes occur”. 2. Save the changes.
3. Then once again under Settings click Build Triggers.
4. Change the status to on.
5. Copy the example curl command – i.e., $ curl --data "build=true" -X POST https://registry.hub.docker.com/u/jrgcubano/docker-play/trigger/43ad1511-5cdb-42aa-9bd7-b736c868c16f/.

## CircleCI (2)
1. Within the Project Settings, select Environment variables.
2. Add a new variable with the name “DEPLOY” and paste the curl command as the value.
3. Add this code to the bottom of circle.yml:
```javascript
deployment:
  hub:
    branch: master
    commands:
      - $DEPLOY
```
This simple fires the $DEPLOY variable after our tests pass on the master branch.

## Final test
1. Create a new branch
2. Make changes locally
3. Issue a pull request
4. Manually merge once the tests pass
5. Once the second round passes, a new build is triggered on Docker Hub

##Deployment
