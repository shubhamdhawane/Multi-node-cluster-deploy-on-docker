# Multi-node-cluster-deploy-on-docker

Introduction to Apache Cassandra
Apache Cassandra is an open-source, distributed NoSQL database management system designed to handle large amounts of data across many commodity servers,
providing high availability with no single point of failure

Today's lab session focused on Apache Cassandra! We will install and run the basic commands to containerise and create a cluster of Cassandra NoSQL nodes.

# Focuses on how to:
* Configure Cassandra containers on aws ec2
* Run a Cassandra cluster
* Run the basic commands to interact with Cassandra

# Phase 1: Setting up our environment
we will need a ec2 instance. I used the following configuration.
* Machine type: t2-large
* HTTP traffic: On
* HTTPS traffic: On
* Image: ubuntu-20.04
* Size (GB): 30

  Lets start

Let's update our system.

```bash
sudo apt-get update
```

 We can now install Docker; make sure you type `Y` for Yes when prompted.
```bash
sudo apt-get install docker.io -y
```

 What is the Docker version? Run the next command
```bash
sudo docker --version
```

 Let's create a new user called docker-user. You can use this user to run our containers.
```bash
sudo adduser docker-user
```

 We will need to give `sudo` access to our new `docker-user`, so let's add it to the `sudo` group.
```bash
sudo usermod -aG sudo docker-user
```
> If we don't add the user in the `sudo` group, we will not run `sudo` commands.

 Now, run the following command; this will allow us to give `sudo` permissions to docker to run our commands. 

```bash
sudo usermod -aG docker docker-user
```

> This command ensures that our new `docker-user` can run docker commands without using the `sudo` keyword. For example, instead of running always: 
>
> ```bash
> $ sudo docker <command> 
> ```
>
> we will be able to run:
>
> ```bash
> $ docker <command>
> ```

 Let's switch users, type the following command.

```bash
su - docker-user
```
* We should be ready now! Try the following command to see if everything works fine.

```bash
$ docker
```

* You should be able to see a list of available options and commands. We can always refer to this when we need to explore using commands and options.

#### Phase 2: Running a Cassandra container

 Our next step is to create a create a Cassandra container.

* Let us run a new Casandra node using Docker, we can name the new container `my-cassandra-1`.

```bash
docker run --name my-cassandra-1 -m 2g -d cassandra:3.11
```

> The option `-m 2g` will assign 2GB of memory in this container.

> We just created an Apache Cassandra container `my-cassandra-1; let us check the active containers running the following command.

```bash
docker ps -a
```

> The container is up and running!

* Let's stop the container and create our first cluster.

```bash
docker stop my-cassandra-1 
```

* Then delete it.

```bash
docker rm my-cassandra-1 
```
#### Phase 3: Building a three-nodes Cassandra cluster

13. We will create a cluster of three Cassandra nodes; the first Cassandra node will be called `cassandra-1`.  we will use Cassandra  3.11.

* We will interact with the cluster using the `nodetool`.
* The `nodetool` utility is a command-line interface for managing a Cassandra cluster. The Cassandra cluster will work as one unique database system to manipulate data.

```bash
docker run --name cassandra-1 -d cassandra:3.11
```

> Using the `docker ps -a command`, you can check if your container is up and running.
>
> It is good to check if the container is up and running each time we create one. 

* Let us inspect `cassandra-1`.

```bash
docker inspect cassandra-1
```

