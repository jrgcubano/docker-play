# docker-play
Playing with docker, continuous integration and automatic deploy

Original article [Node With Docker - Continuous Integration and Delivery](http://mherman.org/blog/2015/03/06/node-with-docker-continuous-integration-and-delivery/#.VQ9YhZPF-6B).


# Steps:

## Install
> brew update
> brew install docker
> brew install boot2docker
> brew install docker-composer

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

$exports (do exports)

## Example project

### Example docker file (Dockerfile)
inside app/Dockerfile
insede redis/Dockerfile

### Example docker-composer file (docker-example.yml)
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

$docker-compose up (get and start all the dependencies defined in Dockerfiles)




