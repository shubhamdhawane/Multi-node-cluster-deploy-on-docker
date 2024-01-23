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

We can now install Docker; make sure you type `Y` for Yes when prompted.
```bash
sudo apt-get install docker.io -y

 What is the Docker version? Run the next command
```bash
sudo docker --version

 Let's create a new user called docker-user. You can use this user to run our containers.
```bash
sudo adduser docker-user

 We will need to give `sudo` access to our new `docker-user`, so let's add it to the `sudo` group.
```bash
sudo usermod -aG sudo docker-user
