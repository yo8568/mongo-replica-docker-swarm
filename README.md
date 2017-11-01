# mongo-docker-swarm

Running Mongo DB Replica Set on three different server by Docker Swarm

## Server Requirements

1. Docker (version >= 17.0.0)
1. Docker-Compose

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

## Installation Steps

### Initialize Manager Node

    docker swarm init --listen-addr *manager-address:2377* --advertise-addr *manager-address*

*Output*

    $ docker swarm init --advertise-addr *manager-address*
    Swarm initialized: current node (bvz81updecsj6wjz393c09vti) is now a manager.

    To add a worker to this swarm, run the following command:

        docker swarm join \
        --token *Docker Swarm Token* \
        *manager-address*

    To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.

### Initialize Worker Node

    ssh <-- The other servers -->
    docker swarm join --token *Docker Swarm Token* *manager-address*

### Setup Mongo DB on each node

First, you should update label on each node.

    ssh <-- The manager node server -->
    docker node update --label-add mongo.replica=1 *manager-address*
    docker node update --label-add mongo.replica=2 *worker1-address*
    docker node update --label-add mongo.replica=3 *worker2-address*

Second, deploy service on manager node.

    ssh <-- The manager node server -->
    docker stack deploy -c docker-compose.yml overlay

    docker service update --publish-add 27017:27017 overlay_mongo1

And, you should login *Manager node* and run the under command.

    ./initialte-manager

## Persistent storage

Data is stored at `./data` and is excluded from version control. Data will be persistent between container runs. To remove all data `./reset`

## Reference

- [Docker Swarm Document](https://docs.docker.com/engine/reference/commandline/swarm_init)
- [Learning note about Docker Swarm Mode](http://www.evanlin.com/til-2016-07-13/)
- [Mongo Documentation - mongod](https://docs.mongodb.com/manual/reference/program/mongod/)
- [Running a MongoDB Replica Set on Docker 1.12 Swarm Mode: Step by Step](https://medium.com/@kalahari/running-a-mongodb-replica-set-on-docker-1-12-swarm-mode-step-by-step-a5f3ba07d06e)
