Run your first Docker container
===============================

# Description

In this lab you'll get to know how to use the Docker Machine tool, how to search for Docker images on Docker hub, and how to run your images and connect to the applications inside them.

## Lab setup verification

Verify that you have the following software installed:

1. A 64-bit OS (Docker for Windows requires Windows 10 Professional)
2. [Docker CE](https://store.docker.com/search?offering=community&platform=desktop&q=&type=edition)
Select your OS, this will guide you through downloading and installing on Windows or
Please select stable as the distro you will use.
If you feel your advanced you can select edge but expect outputs to differ from what we build.

(Sidenote: If you want to get into managing your apps in a more interesting way have a look at [Homebrew](http://brew.sh/) and [Homebrew Cask](http://caskroom.io/) for OS X, and [Chocolatey for Windows](https://chocolatey.org/packages/docker).)

Docker Machine will create a virtual machine for us where we can run our docker containers, since you cannot run Docker containers or bare OS X or Windows. This part isn't needed if you're running a Linux desktop.

Let's set up our first container host!


Install the Docker CE (Community Edition) Engine and make sure it starts.
Windows
![inline](screenshot-docker-win.PNG)

Mac
![inline](Screenshot2017-04-2614.47.45.png)
Check for the Moby icon in your system tray.

You now have a container host (your laptop) ready to use, let's make sure we can connect to it.

## Using Docker

Now that you have a container host there are several things you can do.

If you just run the command `docker` you'll get a list of all the commands you can issue.

First off we want to have a look at the Docker installation information, so run:

`docker info`

This should give you an output similar to this:

```
$ docker info
Containers: 0
Images: 0
Storage Driver: aufs
 Root Dir: /mnt/sda1/var/lib/docker/aufs
 Backing Filesystem: extfs
 Dirs: 0
 Dirperm1 Supported: true
Execution Driver: native-0.2
Logging Driver: json-file
Kernel Version: 4.0.9-boot2docker
Operating System: Boot2Docker 1.8.1 (TCL 6.3); master : 7f12e95 - Thu Aug 13 03:24:56 UTC 2015
CPUs: 1
Total Memory: 996.2 MiB
Name: containerhost
ID: 4ZKQ:2L2G:SY3X:XQUN:DQEE:OSWN:4JL2:5WIW:SKGO:DG2Z:YZAU:ORYB
Debug mode (server): true
File Descriptors: 9
Goroutines: 16
System Time: 2015-09-17T16:12:42.43394229Z
EventsListeners: 0
Init SHA1:
Init Path: /usr/local/bin/docker
Docker Root Dir: /mnt/sda1/var/lib/docker
Username: virtualswede
Registry: https://index.docker.io/v1/
Labels:
 provider=virtualbox
```

Here we can see how many images we have downloaded,  how many containers we have running, and more info like version of Docker etc.

The second command we'll look at is searching for images that we find interesting.

The [Docker hub](https://hub.docker.com) currently has approximately **100,000+** images available for everyone to use freely.

Try a search for Sensu, a [really cool monitoring solution](http://sensuapp.org):

`docker search sensu`

You should get a list of Docker images that have been published and shared by the community, like this:

```
$ docker search sensu
NAME                                  DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
hiroakis/docker-sensu-server                                                          6                    [OK]
steeef/sensu-centos                   Sensu server on CentOS 6.x Forked from htt...   1                    [OK]
sstarcher/sensu-docker                                                                0                    [OK]
```

Try some other searches, like for redis, apache, nginx, mysql, postgresql, alpine, ubuntu you name it, try to see if there are Docker images out there. Make note that any Windows services have to run on a machine with a Windows kernel, aka Windows 10 or 2016. If you are on a windows machine try seeing how you can switch from Linux based containers to windows containers. If you find an application or service that's not on the Docker hub, perhaps you could create one and be the first! Community FTW!

Now let's try to download an image using the `docker pull` command:

`docker pull ubuntu`

You'll see that Docker starts downloading a Docker image, this specific one is a base Ubuntu image. We'll try other images with more applications later.

When download is complete, your "Hello World" with Docker command looks something like this:

`docker run ubuntu echo hello world`

You can run other commands or invoke a bash shell like this:

`docker run -i -t ubuntu /bin/bash`

What does this command do? Check the documentation for a specific Docker command by running it without any options, like:

`docker run`

You'll see that `-i` means we'll run this container interactively, and `-t` means we're attaching a pseudo-TTY to it (essentially attaching a terminal), then giving Docker the name of the image we want to run, and a command within that image.

This will start the container, giving it its own filesystem, networking etc. and start up a shell for us to rummage around inside the container. Pretty cool!

Now that you're in your container, look around a bit and run some commands, like `ifconfig` and `ls /`. You can even install applications here, so try to run `apt-get update` and then `apt-get install apache2`.

You now have apache running in the container!

To exit the container, there are two options. `CTRL-d` exits the shell and shuts down the container. If you want to keep the container running but detach from it, do `CTRL-p CTRL-q`. You can then re-attach to it by running `docker attach UNIQUEID`.

## Run some simple apps

We will run tomcat which a Java based application server. To do this we use the following command.

```docker run -d -p 8888:8080 tomcat:8.0```

The image will be downloaded if required and tomcat will be run in a *deamon* mode.

Now run the following command.

```docker ps```

Which will yield an output that looks something like below (Partial output).

```
STATUS              PORTS                    NAMES
Up 17 seconds       0.0.0.0:8888->8080/tcp   romantic_stallman
```
Notice that under the PORTS section the port 8888 of the localhost is forwarded to port 8080 of tomcat running in the container.

You can run your apps by pointing your browser to ```localhost:8888``` or you can just verify using the following curl commands.

```curl -L $(docker-machine ip containerhost 2>/dev/null):8888```

```curl -L $(docker-machine ip containerhost 2>/dev/null):8888/examples```

## Build your own container

Building containers is easy, and we'll show how to by using a Dockerfile.

A Dockerfile is just a simple textfile with information about what to start from (a linux distro usually), what commands to run before it's complete (installing packages and changing configuration files for instance), what port to open and lastly what binary to run.

There are more settings of course, and we'd recommend you to read up on them [here](https://docs.docker.com/).

An easy container to build is the Redis key-value store service. Read through the steps [outlined here](https://docs.docker.com/examples/running_redis_service/).

## Troubleshooting

#### Environment variables

If you get an output like the one below:

```
$ docker ps
Cannot connect to the Docker daemon. Is 'docker -d' running on this host?
```

Make sure you have the correct environment variables exported as outlined above.

#### Certificate and Trust Issues
There are two encryption paths that must be considered.  There is TLS encryption going from the Docker client running on your laptop to the container host Docker daemon.  There is also TLS encryption running from the Docker daemon within the container host to the public Docker Hub/CDN.

If you have problems with your client there are likely two issues.

1. Check that you can reach the container host by checking the IP with `ping $(docker-machine ip containerhost)`.  If this doesn't work then you probably need to add a route for the `192.168.99.x` traffic to the proper VBoxNet interface.  This issue is usually caused by Cisco VPN so make sure you're disconnected from the VPN and some times a proper restart is needed.  Run an `ifconfig` to identify which is the proper interface and then run ```sudo route -n add -net 192.168.99.0/24 -interface vboxnetX``` for the proper interface.
>As a side note, you can communicate to the container host via a `NAT` and `BRIDGED` method.  The `NAT` method means that you can leverage the `127.0.0.1` address of your laptop and it forwards appropriate ports to a static IP of your container host.  The method we are using for this workshop is the `BRIDGED` mode and uses a `routed` IP.


#### General Troubleshooting
One way to troubleshoot is to run the Docker daemon manually to see the logs in real-time.  
1. Manually run the Docker daemon with the command you found from ```ps``` which should be something similar to ```/usr/local/bin/docker -d -D -g /var/lib/docker -H unix:// -H tcp://0.0.0.0:2376 --tlsverify --tlscacert=/var/lib/boot2docker/tls/ca.pem --tlscert=/var/lib/boot2docker/tls/server.pem --tlskey=/var/lib/boot2docker/tls/serverkey.pem```.
2. Make sure to leave this process ```running``` and open another window for other operations.


If docker-machine is hung on "starting vm..." and you're on a Mac, it might be due to a conflict with Kitematic (if you have it installed). Steps to resolve:

1. Ctrl+C out of it.
2. Delete the new machine ```docker-machine rm containerhost```
3. Restart the virtualbox interface ```sudo ifconfig vboxnet0 down``` and then ```sudo ifconfig vboxnet0 up```
4. Lastly recreate it again: ```docker-machine create --driver virtualbox containerhost```
