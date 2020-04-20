
# Docker with MOhammad Dehnavi <m.dehnavi@respina.net>

# Table of Contents

- [Installation](#introduction)
   - [Installation Docker Engine](#Docker-Engine)
     - [Install requirement](#install-requirement)
     - [Add gpg key to apt](#Add-gpg-key-to-apt)
     - [Add docker repo to apt](#Add-docker-repo-to-apt)
     - [Update apt and install docker engine](#Update-apt-and-install-docker-engine)
     - [Run hello world](#Run-hello-world)
   - [Installation Docker-compose](#Docker-compose-install)
   - [Run docker command without sudo](#Run-docker-command-without-sudo)
- [Most useful docker commands](#Most-useful-docker-commands)
   - [Run](#Run) 
   - [PS](#PS)
   - [Stop and Kill](#Stop-and-Kill)
- [Dockerfile](#Dockerfile)
   - [Create Dockerfile](#Create-Dockerfile)
   - [Build Dockerfile](#Build-Dockerfile)
- [Docker-compose](#Docker-compose)

# Installation
#### Docker-Engine
In this toturial we use ubuntu 18.04  
First you should remove old version of docker 
```
sudo apt-get remove docker docker-engine docker.io containerd runc
```
install with apt package manager  
[We use this installation Doc from docker.com](https://docs.docker.com/engine/install/ubuntu/)

#### install requirement
```
sudo apt-get update
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
```

#### Add gpg key to apt
```    
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

#### Add docker repo to apt 
```
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

#### Update apt and install docker engine
```
sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io
```

#### Run hello world
Verify that Docker Engine is installed correctly by running the hello-world image 
```
sudo docker run hello-world
```

#### Docker-compose-install
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.25.5/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose

docker-compose --version
```
#### Run docker command without sudo
If you want run docker command without sudo add your user to docker group 
```
sudo groupadd docker 
sudo usermod -aG docker $USER
```
# Most useful docker commands

#### Run
Run command is the best command for pull and start a container
```
docker run <image name:tag>

docker run ubuntu # pull image of ubuntu:latest 
```
- Note: Docker default tag is latest and if you don't use tag the default tag used by docker engine

- Note: First time docker pull image from hub.docker.com and after that we can use image localy

- Note: docker run command is equal with ==>  docker create + docker start

```
docker run <image name> <command to override>  #override startup command

docker run ubuntu ls  # run container with ls command
````
```
docker start -a <container-ID> # run exited container
```
```
docker run --rm <image name> # container deleted after exite from that when we use --rm in run command 
```
```
docker run -p <machinePort>:<ContainerPort> <image name> # map container port to local machine port
```
```
docker exec -it <container-ID> <command> # run command in a running container 

docker exec -it <container id> bash # for sample this command give a shell for us in the running container
```
- Note: In docker run command <-it> option allow us to go interactive mode

#### PS
Ps command is for show list of containers
```
docker ps # show list of all running container

docker ps -a # Show all containers(runnig, exited container)
```
#### Stop and Kill
If you want to stop a container you can use stop command, this command send sigterm signal and if container don't stop then after 10 second send kill signal and kill container

- Note: You can use kill command directly!!

```
docker stop <container-ID> # stop command
docker kill <container-ID> # kill command
```

# Dockerfile
A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image and with docker build command we can create own docker image. You can see a sample Dockerfile in the below

#### Create Dockerfile
create a folder and put your source files to it then create a file with name Dokcerfile.
```
# This is docker file for run web app with nodeJS
FROM node:alpine # Define base image
WORKDIR /mohammad/webapp # Define working directory, if folder don't exist doker create that folder. 
COPY ./package.json ./ # copy file from host to container
RUN npm install # run the npm install command
COPY ./ ./ # copy file from host to container
CMD ["npm", "start"] # Define default command when we run created image. 
```
- Note: You can use ADD for copy file from host to machine, Add have built in tar function to extracted files and copy to container
- Note: Every COPY,RUN,ADD create a read only layer when we build own image.

#### Build Dockerfile
You can build your docker image with a Dockerfile use the below command. 

```
# Build image with docker build command
docker build -t <Your-Image-Name> -f <Dockerfile-name> . 
```
# Docker-compose
If you have a app with multiple service you can use docker-compose for runnig your app with multiple container. in the below you can see a sample docker-compose file for running wikijs. 
create a folder and put your source code in that folder and create a yaml file with name docker-compose.yml

```
version: "3" # this is docker-compose interpreter version
services: #  define a service 

  db: # container host name
    image: postgres:11-alpine # base image 
    environment:
      POSTGRES_DB: wiki
      POSTGRES_PASSWORD: wikijsrocks
      POSTGRES_USER: wikijs
    logging:
      driver: "none"
    restart: unless-stopped
    volumes: # define volume for store database localy
      - db-data:/var/lib/postgresql/data

  wiki: # container host name
    image: requarks/wiki:2 # define base image
    depends_on: # dfine dependency for running this container
      - db
    environment:
      DB_TYPE: postgres
      DB_HOST: db
      DB_PORT: 5432
      DB_USER: wikijs
      DB_PASS: wikijsrocks
      DB_NAME: wiki
    restart: unless-stopped
    ports: # expose port for access localy
      - "80:3000" 

volumes:
  db-data:
```
- Note: docker-compose syntax is yaml. 
