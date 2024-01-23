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
$ sudo apt-get update
