# **Docker Cheat Sheet**

## Docker Container
- `docker container run` : Start up docker container and run
  - `--publish, -p` : Publish a container’s port(s) to the host
  - `--detach, -d` : Run container in background and print container ID
  - `--name` : Assign name to container
  - `--network` : Connect to specified network
  - `-it` : Start new container interactively
    - ex) `docker container run -it --name ubuntu ubuntu` : Start up docker with ubuntu and current terminal show as ubuntu
    - ex) `docker container run -it --name proxy nginx bash` : Start container named 'proxy' with nginx installed, and open bash shell to interact with the container
  - `--rm` : Automatically remove the container when it exits
  - `--net-alias` : Add network-scoped alias for the container.
    - ex) `docker container run -d --net my_net --net-alias search elasticsearch:2` : This container will be able to be searched by DNS `search` under the network `my_net`. To search, `docker container run --rm --net my_net alpine nslookup search` is going to run new container that is connected with my_net network and look for DNS `search`
  - `--volume, -v` : Bind mount a volume.
    - `-v, --volume=[host-src:]container-dest[:<options>]`
  - ex) `docker container run -p 80:80 --rm <IMAGE_NAME>` : Run container using the <IMAGE_NAME>

- `docker container exec -it` : Run additional command in **existing container**. Does exactly the same as `docker container run -it` but only on existing container. Doesn't start a new one.
  - ex) `docker container exec -it <CONTAINER_NAME> bash`

- `docker container start` : Start docker container
  - `docker container start -ai <CONTAINER_NAME>` : Start existing container
    - ex) `docker container start -ai ubuntu`
- `docker container stop` : Stop docker container
- `docker container rm <CONTAINER_ID>` : Remove container

- `docker container ls` : list running docker containers info
- `docker container ls -a` : list whole docker containers info
- `docker ps` : list docker containers info

- `docker container logs -f <CONTAINER_NAME>` : logs of specified container. `-f` option will keep watch the logs as it runs.
- `docker top <CONTAINER_NAME>` : list running processes in CONTAINER_NAME
- `docker container inspect` : details of one container config. Shows metadata about the container(startup config, volumes, networking, etc)
  - `docker container inspect --format '{{ .NetworkSettings.IPAddress }}' <CONTAINER_NAME>` : shows the IP address of selected container
- `docker container port <CONTAINER_NAME>` : shows the port numb for selected container
- `docker container stats` : performance stats for all containers

- `docker container help` : list docker container commands

## Docker Network
- `docker network ls` : List all networks
- `docker network inspect <NETWORK_NAME>` : Shows info about NETWORK_NAME. Can see which containers are connected to the network.
- `docker network create <NETWORK_NAME>` : create new network
  - `--driver` : specify driver. Default is bridge
- `docker network connect <NETWORK_ID> <CONTAINER_ID>` : connect the selected container to specified network
- `docker network disconnect <NETWORK_ID> <CONTAINER_ID>` : disconnect the container from NETWORK_ID

## Docker Image
: Docker image is made of multiple layers which have their own unique SHA. Using the unique ID of it, already existing layer will not be downloaded again because it's being cached. A container is just a single read/write layer on top of image.
- `docker image ls` : List all docker images
- `docker image history <IMAGE>` : Show layers of changes made in image
- `docker image inspect <IMAGE>` : returns JSON metadata about the image
- `docker image tag <SOURCE_IMAGE>[:TAG] <TARGET_IMAGE>[:TAG]` : Assign one or more tags to an image. Tag will be latest as default

## Dockerfile
: Ordering of the lines in Dockerfile is important because the ones that are not changed will be re-used, but after the one that's been changed, every line will be triggered again. Hence, the part that changes the least should be placed at the front part of Dockerfile, while the part that changes the most should be placed at the bottom.
- `FROM` :  Initializes a new build stage and sets the Base Image for subsequent instructions.
- `ENV` : Environment variables
- `WORKDIR` : Changing working directory. Using `WORKDIR` is preferred to using `RUN cd <PATH>` because it shows what you're doing in Dockerfile
- `COPY` : Copies new files or directories from <src> which is from host, and adds them to the filesystem of the container at the path <dest>
  - `COPY [--chown=<user>:<group>] <src>... <dest>`
   ex) `COPY package.json package.json` : copy package.json from the current directory of host and paste it on container working directory.
   `COPY . .` : copy the whole current directory to working directory on the container
- `EXPOSE` : Container listens on the specified network ports at runtime
- `RUN` : Execute commands in a new layer on top of the current image and commit the results
  - `RUN <command>` : (shell form, the command is run in a shell, which by default is /bin/sh -c on Linux or cmd /S /C on Windows)
  - `RUN ["executable", "param1", "param2"]` : (exec form)
  - In the shell form a \ (backslash) can be used to continue a single RUN instruction onto the next line. For example, consider these two lines: `RUN /bin/bash -c 'source $HOME/.bashrc; \
echo $HOME'`
- `CMD` : Runs every time new container launches or restarts from the image. Only one `CMD` is allowed. If there's multiple, only the last one will be executed.
- `docker image build -t <TAG_NAME> .` : Build the image in this directory

## Docker Volume
: Docker data volume is storing data in host, *NOT* in containers. Unlike file or data changes on docker container that is being controlled by `Union File System`, data volume is being stored on the host and is not controlled by `Union File System`. Therefore, it can be used when multiple containers have to share data. Even though we delete the containers, since volumes are being stored on the host machine, it's not being deleted.
- ex) `Bind mount` : `docker container run -v /root/data:/data` : Linking `/root/data` directory of Host to `/data` directory of Docker container. To use current directory of Host, use `$(pwd):<CONTAINER_DIR_PATH>`. Difference from `Named Volume` is that it starts with slash.
- ex) `Named Volume` : `docker container run -v mysql-db:/var/lib/mysql` : Adding name to the volume. Can be checked by `docker volume ls`
- `docker volume create` : Required to do this before `docker run` to use custom drivers and labels.

## Docker Compose
: Instead of using docker run commands to control multiple containers each time, by using docker compose, it's much easier to handle those.
- `docker compose up` : setup volumes/networks and start all containers
- `docker compose down` : stop all containers and remove cont/vol/net.
  - `docker compose down -v` : Remove the named volumes
- Template :

```
version: '3.1'  # if no version is specified then v1 is assumed. Recommend v2 minimum

services:  # containers. same as docker run
  servicename: # a friendly name. this is also DNS name inside network
    image: # Optional if you use build:
    command: # Optional, replace the default CMD specified by the image
    environment: # Optional, same as -e in docker run
    volumes: # Optional, same as -v in docker run. '.' is the same as $(pwd) from docker run.
  servicename2:
    build: # Build an image and use the image for this servicename2
      context: . # Use Dockerfile in current directory
      dockerfile: # Dockerfile name
    image: # Name of the new image

volumes: # Optional, same as docker volume create

networks: # Optional, same as docker network create
```

## Docker Hub
- Login credential auth key is being stored under `.docker/config.json`

- copy-on-write :


## References
- [Docker documents](https://docs.docker.com/engine/reference/run/#general-form)
- [Udemy Docker mastery](https://www.udemy.com/docker-mastery)
