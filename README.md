# multi-node-cassandra-docker
Multi-node Cassandra setup using docker with minimal configuration

This setup helps spawning up a 2 node cassandra cluster with default configuration and minimum configuration changes.

## Prerequisites
Ensure you have [Docker Desktop](https://www.docker.com/products/docker-desktop) installed. The definitions in this directory have been verified to work with the following setup.

![](./img/docker-desktop-version.png)

## Starting Up The Containerized Environment
```
cd multi-node-cassandra-docker
docker-compose up
```

## Ensuring you have the right setup
SSH into the running using: 
```
docker exec -it cassandra_node_1 bash
```

Once inside the container run:

```
nodetool status
```
you should be able to see two hosts running.

## Useful Commands For Working With Docker Containers
|Description|Command|
|---|---|
|Start all containers defined in the `docker-compose.yml` of your current directory. Force the container image to be rebuilt.|`docker-compose up --build`|
|Stop all containers defined in the `docker-compose.yml` of your current directory.|`docker-compose down`|
|List all running containers.|`docker ps`|
|Display in CPU/Memory/I/O stats for all containers|`docker stats`|
|Stop/Start/Restart a specific container by Container ID or Container Name.|`docker stop <Container ID or Container Name>`<br>`docker stop <Container ID or Container Name>`<br>`docker restart <Container ID or Container Name>`|

Below are some functions that can be added to `~/.bash_profile` for quickly resetting your environment.

```
###########################
##### DOCKER COMMANDS #####
###########################

### Stop and wipe the containers defined in the current directory's docker-compose.yml file. ###
docker_compose_wipe() {
    docker-compose ps -q | xargs -I% docker-compose stop %
    docker-compose ps -aq | xargs -I% docker-compose rm %
    docker-compose volume prune
}

### Stop and wipe the containers defined in the current directory's docker-compose.yml file. Then start a fresh set of containers. ###
docker_compose_reset() {
    docker_compose_wipe
    docker-compose up --build
}

### Stop ALL containers, including those that aren't included in the current directory's docker-compose.yml file. ###
docker_stop_all() {
    docker ps -aq | xargs -I% docker stop %
}


### Stop and wipe ALL containers. ###
docker_wipe_all() {
    docker ps -q | xargs -I% docker stop %
    docker ps -aq | xargs -I% docker rm %
    docker volume prune
}


### Stop and wipe ALL containers. Remove all containers images and volumes as well. ###
docker_armageddon() {
    docker_wipe_all
    docker rmi -f $(docker images --filter dangling=true -qa)
    docker volume rm $(docker volume ls --filter dangling=true -q)
}
```

