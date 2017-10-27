# mongo-cluster
This repository provides a fully sharded mongo environment using docker-compose and local storage.

The MongoDB environment consists of the following docker containers

 - **mongosrs(1-2)n(1-3)**: Mongod data server with 2 replica sets containing 3 nodes each (6 containers)
 - **mongocfg(1-3)**: Stores metadata for sharded data distribution with 1 replica sets containing 3 nodes each (3 containers)
 - **mongos(1-2)**: Mongo routing service to connect to the cluster through (1 container)

## Installation

### Install Docker

    sudo apt-get install -y apparmor lxc cgroup-lite curl
    wget -qO- https://get.docker.com/ | sh
    sudo usermod -aG docker YourUserNameHere
    sudo service docker restart

### Install Docker-compose

    sudo su
    curl -L https://github.com/docker/compose/releases/download/1.4.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
    chmod +x /usr/local/bin/docker-compose
    exit

### Check out the repository

    git clone <-- your repository address -->
    cd mongo-cluster


### Setup Cluster
This will pull all the images from [Docker index](https://index.docker.io/u/jacksoncage/mongo/) and run all the containers.

    docker-compose up

Please note that you will need docker-compose 1.4.2 or better for this to work due to circular references between cluster members.
You will need to run the following *once* only to initialize all replica sets and shard data across them

    ./initiate

You should now be able connect to mongos1 and the new sharded cluster from the mongos container itself using the mongo shell to connect to the running mongos process

    docker exec -it mongos1 mongo --port 21017

## Persistent storage
Data is stored at `./data/` and is excluded from version control. Data will be persistent between container runs. To remove all data `./reset`

## Reference
  - [mongo-docker-compose](https://github.com/singram/mongo-docker-compose)
  - [Mongo Documentation - mongod](https://docs.mongodb.com/manual/reference/program/mongod/)
  - [Mongo DB Sharding Structure] (https://blog.toright.com/posts/4552/mongodb-sharding-%E5%88%86%E6%95%A3%E5%BC%8F%E5%84%B2%E5%AD%98%E6%9E%B6%E6%A7%8B%E5%BB%BA%E7%BD%AE-%E6%A6%82%E5%BF%B5%E7%AF%87.html)
