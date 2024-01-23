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

We will create a cluster of three Cassandra nodes; the first Cassandra node will be called `cassandra-1`.  we will use Cassandra  3.11.

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
>  The output shows the configuration parameters of our container; if we want to extract a particular value, we can use the following command.
>
> * The command extracts the `IPAddress` of container `cassandra-1`

```bash
$ docker inspect --format='{{ .NetworkSettings.IPAddress }}' cassandra-1

# The output is the container IP address
# 172.17.0.2
```
There are different ways to create a cluster; the most common practice is to set up a cluster configuring the IP addresses of containers or VMs. Since we run everything in the same VM, we can use the container names rather than IPs.
    * Before we proceed, let's make sure that our container is up and running. It should be up and running as we extracted the IP address.

```bash
$ docker ps -a
```
 Let's use the `nodetool` command in `cassandra-1` to check if our Cassandra node is up and running.

```bash
$ docker exec -i -t cassandra-1 bash -c 'nodetool status'
```
> * The output should look like this:
>
> ```bash
> Datacenter: datacenter1
> =======================
> Status=Up/Down
> |/ State=Normal/Leaving/Joining/Moving
> --  Address     Load       Tokens       Owns (effective)  Host ID                               Rack
> UN  172.17.0.2  100.22 KiB  256          100.0%            abc2a2ee-9bff-415f-8cd0-f8a19295e846  rack1
> ```
>
> * The output shows that our container is in `UN` status ( Up and Normal) :happy:

16. Let's connect our second container. Run the following command to create the second Cassandra node `cassandra-2`. 

```bash
$ docker run --name cassandra-2 -d --link cassandra-1:cassandra cassandra:3.11
```

> * We used the `--link cassandra1:cassandra` option to link `cassandra-1` to `cassandra-2`. This will create our cluster.

17. Let us check the status of the containers.

```bash
$ docker exec -i -t cassandra-1 bash -c 'nodetool status' 
```

> * The output is:
>
> ```
> Datacenter: datacenter1
> =======================
> Status=Up/Down
> |/ State=Normal/Leaving/Joining/Moving
> --  Address     Load       Tokens       Owns (effective)  Host ID                               Rack
> UJ  172.17.0.3  30.47 KiB  256          ?                 bff8c5c1-8af3-4eb9-bfce-a6f90c049972  rack1
> UN  172.17.0.2  70.9 KiB   256          100.0%            abc2a2ee-9bff-415f-8cd0-f8a19295e846  rack1
>```
>
> * If you see a question mark (**?**) in **Owns**, that’s fine!
> * You might notice that the status is `UJ` , which means Up and in Join (not yet in Normal status).
> * Wait for the containers to get synchronised; this will take a minute or two, then rerun the same command; the output should look like this:
> >
> ```
> Datacenter: datacenter1
> =======================
> Status=Up/Down
> |/ State=Normal/Leaving/Joining/Moving
> --  Address     Load       Tokens       Owns (effective)  Host ID                               Rack
> UN  172.17.0.3  70.92 KiB  256          100.0%            bff8c5c1-8af3-4eb9-bfce-a6f90c049972  rack1
> UN  172.17.0.2  75.93 KiB  256          100.0%            abc2a2ee-9bff-415f-8cd0-f8a19295e846  rack1
> ```
>
> * Both containers are up and running in normal state (`UN`) as part of the same datacenter.

