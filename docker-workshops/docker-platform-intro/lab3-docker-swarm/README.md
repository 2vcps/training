Create a cluster of container hosts
===============================

# Description

In this lab you'll use Docker Machine to set up a "swarm" of container hosts, a cluster that you can manage and schedule containers on top of.

## The Docker Swarm master using Swarm mode

We need a Swarm Master that will be our main point of contact for the entire cluster.
First we need to create a discovery token, and we can use our existing container host to create one for us:

```
$ docker swarm init
Swarm initialized: current node (b0bz8qaotyuajv0d9pjzq38a4) is now a manager.

To add a worker to this swarm, run the following command:

    docker swarm join \
    --token SWMTKN-<this will be unique per swarm> \
    192.168.65.4:2377

To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.


```
We have created a single node swarm. Good enough for the lab but can you see what to do to add more manager or worker nodes?

How can we see our Swarm cluster?

```
$ docker node ls
ID                           HOSTNAME  STATUS  AVAILABILITY  MANAGER STATUS
b0bz8qaotyuajv0d9pjzq38a4 *  moby      Ready   Active        Leader
```

Let's have a look at `docker info`:

```
$ docker info
Containers: 11
 Running: 0
 Paused: 0
 Stopped: 11
Images: 93
Server Version: 17.03.1-ce
Storage Driver: overlay2
 Backing Filesystem: extfs
 Supports d_type: true
 Native Overlay Diff: true
Logging Driver: json-file
Cgroup Driver: cgroupfs
Plugins:
 Volume: local
 Network: bridge host ipvlan macvlan null overlay
Swarm: active
 NodeID: b0bz8qaotyuajv0d9pjzq38a4
 Is Manager: true
 ClusterID: ocqwdl6h6ve83y2qc4jlxugnh
 Managers: 1
 Nodes: 1
 Orchestration:
  Task History Retention Limit: 5
 Raft:
  Snapshot Interval: 10000
  Number of Old Snapshots to Retain: 0
  Heartbeat Tick: 1
  Election Tick: 3
 Dispatcher:
  Heartbeat Period: 5 seconds
 CA Configuration:
  Expiry Duration: 3 months
 Node Address: 192.168.65.2
 Manager Addresses:
  192.168.65.2:2377
Runtimes: runc
Default Runtime: runc
Init Binary: docker-init
containerd version: 4ab9917febca54791c5f071a9d1f404867857fcc
runc version: 54296cf40ad8143b62dbcaa1d90e520a2136ddfe
init version: N/A (expected: 949e6facb77383876aeff8a6944dde66b3089574)
Security Options:
 seccomp
  Profile: default
Kernel Version: 4.9.13-moby
Operating System: Alpine Linux v3.5
OSType: linux
Architecture: x86_64
CPUs: 4
Total Memory: 1.952 GiB
Name: moby
ID: YNHZ:LTRT:GCAC:TVLV:OY37:LMGE:HLDG:BCIE:LKRS:YKSL:7N2N:HBPG
Docker Root Dir: /var/lib/docker
Debug Mode (client): false
Debug Mode (server): true
 File Descriptors: 35
 Goroutines: 145
 System Time: 2017-04-26T19:14:23.68195471Z
 EventsListeners: 1
No Proxy: *.local, 169.254/16
Registry: https://index.docker.io/v1/
Experimental: true
Insecure Registries:
 127.0.0.0/8
Live Restore Enabled: false

```

You can se we have just one node in our cluster (the Swarm Master), we have 4 CPU and ~2GB of memory we can use. Let's add some more resources!

--must check if we want to build whole clusters during the lab



```
docker service create redis
tw4vu5ig3u1qpauibhwt5plvq
$ docker service create nginx
80zlyrv17w8gf7g8e1myhuxzw
$ docker service create postgres
etz2ga2wz64tojy9b7jc7dqnp

```
What is different from when we did a `docker run -d`?

Now if we look at where those containers are actually running we can see that they are on different nodes (if we had more than one node):

```
$ docker service ls
ID            NAME               MODE        REPLICAS  IMAGE
80zlyrv17w8g  upbeat_murdock     replicated  1/1       nginx:latest
etz2ga2wz64t  frosty_heisenberg  replicated  1/1       postgres:latest
tw4vu5ig3u1q  thirsty_lamport    replicated  1/1       redis:latest

$ $ docker service ps 80zlyrv17w8g
ID            NAME              IMAGE         NODE  DESIRED STATE  CURRENT STATE          ERROR  PORTS
xwnqnwxie7kg  upbeat_murdock.1  nginx:latest  moby  Running        Running 2 minutes ago         

```

There you go, you now have a single (for now) node Docker Swarm cluster up and running on your laptop, with 3 containers scheduled. 
