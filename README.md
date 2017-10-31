# mongo-docker-swarm

## Install Docker

    sudo apt-get install -y apparmor lxc cgroup-lite curl
    wget -qO- https://get.docker.com/ | sh
    sudo usermod -aG docker YourUserNameHere
    sudo service docker restart

## Install Docker-compose

    sudo su
    curl -L https://github.com/docker/compose/releases/download/1.4.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
    chmod +x /usr/local/bin/docker-compose
    exit

## Start to initailize Docker Swarm

### Initailize Manager Node

    docker swarm init --listen-addr `address:2377` --advertise-addr `address`

    *Output*

    ```
    $ docker swarm init --advertise-addr `address`
    Swarm initialized: current node (bvz81updecsj6wjz393c09vti) is now a manager.

    To add a worker to this swarm, run the following command:

        docker swarm join \
        --token `Docker Swarm Token` \
        `address:2377`

    To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.
    ```

## Initailize Worker Node

    docker swarm join --token <-- Docker Swarm Token --> <-- Cluster Manager Node Address -->


### Setup Mongo DB on each node
This will pull all the images from [Docker index](https://index.docker.io/u/jacksoncage/mongo/) and run all the containers.

First, you should setup mongodb container on each node.

    docker-compose up -d

And, you should login *Master node* and run the under command

    ./initialte-manager

## Persistent storage
Data is stored at `./data` and is excluded from version control. Data will be persistent between container runs. To remove all data `./reset`

## Reference
  - [Docker Swarm Document](https://docs.docker.com/engine/reference/commandline/swarm_init)
  - [Learning note about Docker Swarm Mode](http://www.evanlin.com/til-2016-07-13/)
  - [Mongo Documentation - mongod](https://docs.mongodb.com/manual/reference/program/mongod/)
