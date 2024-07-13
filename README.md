# Docker-Notes

## Introduction to Containerization and Docker
- Containerization involves encapsulating or packaging up software code and all its dependencies so that it can run uniformly and consistently on any infrastructure.

Docker can solve the issues like:
- Develop and run the application inside an isolated environment (known as a container) that matches your final deployment environment.
- Put your application inside a single file (known as an image) along with all its dependencies and necessary deployment configurations.
- And share that image through a central server (known as a registry) that is accessible by anyone with proper authorization.

**Now the question is "What role does Docker play here?"**. </br>
It's an open-source containerization platform that allows you to containerize your applications, share them using public or private registries, and also to orchestrate them.

## What is a Container?
A container is an abstraction at the application layer that packages code and dependencies together. Instead of virtualizing the entire physical machine, containers virtualize the host operating system only.

### Virtual Machine vs Container
Just like virtual machines, containers are completely isolated environments from the host system as well as from each other. They are also a lot lighter than the traditional virtual machine, so a large number of containers can be run simultaneously without affecting the performance of the host system.

Containers and virtual machines are actually different ways of virtualizing your physical hardware. The main difference between these two is the method of virtualization.

**Virtual Machine**

![alt text](https://www.freecodecamp.org/news/content/images/2021/04/virtual-machines.svg)

Each virtual machine comes with its own guest operating system which is just as heavy as the host operating system.

The application running inside a virtual machine communicates with the guest operating system, which talks to the hypervisor, which then in turn talks to the host operating system to allocate necessary resources from the physical infrastructure to the running application.

As you can see, there is a long chain of communication between applications running inside virtual machines and the physical infrastructure. The application running inside the virtual machine may take only a small amount of resources, but the guest operating system adds a noticeable overhead.

Unlike a virtual machine, a container does the job of virtualization in a smarter way. Instead of having a complete guest operating system inside a container, it just utilizes the host operating system via the container runtime while maintaining isolation – just like a traditional virtual machine.

**Container**

![alt text](https://www.freecodecamp.org/news/content/images/2021/04/containers.svg)

The container runtime, that is Docker, sits between the containers and the host operating system instead of a hypervisor. The containers then communicate with the container runtime which then communicates with the host operating system to get necessary resources from the physical infrastructure.

As a result of eliminating the entire guest operating system layer, containers are much lighter and less resource-hogging than traditional virtual machines.

## What is Docker Image?
Images are multi-layered self-contained files that act as the template for creating containers. They are like a frozen, read-only copy of a container. Images can be exchanged through registries.

Containers are just images in running state. When you obtain an image from the internet and run a container using that image, you essentially create another temporary writable layer on top of the previous read-only ones.

## What is a Docker Registry?
An image registry is a centralized place where you can upload your images and can also download images created by others. Docker Hub is the default public registry for Docker. Another very popular image registry is Quay by Red Hat.

![alt text](https://www.freecodecamp.org/news/content/images/2021/01/docker-hub.png)

## Docker Architecture Overview
The engine consists of three major components:
- **Docker Daemon**: The daemon (**dockerd**) is a process that keeps running in the background and waits for commands from the client. The daemon is capable of managing various Docker objects.
- **Docker Client**: The client  (**docker**) is a command-line interface program mostly responsible for transporting commands issued by users.
- **REST API**: The REST API acts as a bridge between the daemon and the client. Any command issued using the client passes through the API to finally reach the daemon.

"Docker uses a client-server architecture. The Docker client talks to the Docker daemon, which does the heavy lifting of building, running, and distributing your Docker containers".

![alt text](https://www.freecodecamp.org/news/content/images/2021/01/docker-run-hello-world.svg)

This image is a slightly modified version of the one found in the official docs. The events that occur when you execute the command are as follows:
1. You execute **_docker run hello-world_** command where **hello-world** is the name of an image.
2. Docker client reaches out to the daemon, tells it to get the **hello-world** image and run a container from that.
3. Docker daemon looks for the image within your local repository and realizes that it's not there, resulting in the **Unable to find image 'hello-world:latest'** locally that's printed on your terminal.
4. The daemon then reaches out to the default public registry which is Docker Hub and pulls in the latest copy of the **hello-world** image, indicated by the **latest: Pulling from library/hello-world** line in your terminal.
5. Docker daemon then creates a new container from the freshly pulled image.
6. Finally Docker daemon runs the container created using the **hello-world** image outputting the wall of text on your terminal.

It's the default behavior of Docker daemon to look for images in the hub that are not present locally. But once an image has been fetched, it'll stay in the local cache. 



## Docker Container Manipulation Basics

### 1. How to Run a Container
The generic syntax for this command is as follows:
```shell
docker run <image-name>
```
Prior to version 1.13, Docker had only the previously mentioned command syntax. Later on, the command-line was restructured to have the following syntax:
```shell
docker <object> <command> <options>
```
In this syntax:
-  **object** indicates the type of Docker object you'll be manipulating. This can be a **container**, **image**, **network** or **volume** object.
-  **command** indicates the task to be carried out by the daemon, that is the **run** command.
-  **options** can be any valid parameter that can override the default behavior of the command, like the **--publish** option for port mapping.

Now, following this syntax, the run command can be written as follows:
```shell
docker container run <image name>
```
The image name can be of any image from an online registry or your local system. 

### 2. How to Publish a Port
Containers are isolated environments. Your host system doesn't know anything about what's going on inside a container. Hence, applications running inside a container remain inaccessible from the outside.

To allow access from outside of a container, you must publish the appropriate port inside the container to a port on your local network. The common syntax for the **_--publish_** or **_-p_** option is as follows:
```shell
--publish <host port>:<container port>
```
For example, **_--publish 8080:80_**, it meant any request sent to port 8080 of your host system will be forwarded to port 80 inside the container‌.

### 3. How to Use Detached Mode
Another very popular option of the run command is the **_--detach_** or **_-d_** option.

This is because, by default, containers **_run_** in the foreground and attach themselves to the terminal like any other normal program invoked from the terminal.

In order to override this behavior and keep a container running in background, you can include the **_--detach_** option with the **_run_** command as follows:

```shell
docker container run --detach --publish 8080:80 fhsinchy/hello-dock
# 9f21cb77705810797c4b847dbd330d9c732ffddba14fb435470567a7a3f46cdc
```
The order of the **_options_** you provide doesn't really matter. If you put the **_--publish_** option before the **_--detach_** option, it'll work just the same. One thing that you have to keep in mind in case of the **_run_** command is that the image name must come last. If you put anything after the image name then that'll be passed as an argument to the container entry-point and may result in unexpected situations.

### 4. How to List Containers
The container ls command can be used to list out containers that are currently running. To do so execute following command:

```shell
docker container ls

# CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS                  NAMES
# 9f21cb777058        fhsinchy/hello-dock   "/docker-entrypoint.…"   5 seconds ago       Up 5 seconds        0.0.0.0:8080->80/tcp   gifted_sammet
```
The container ls command only lists the containers that are currently running on your system. In order to list out the containers that have run in the past you can use the --all or -a option.

```shell
docker container ls --all

# CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS                     PORTS                  NAMES
# 9f21cb777058        fhsinchy/hello-dock   "/docker-entrypoint.…"   2 minutes ago       Up 2 minutes               0.0.0.0:8080->80/tcp   gifted_sammet
# 6cf52771dde1        fhsinchy/hello-dock   "/docker-entrypoint.…"   3 minutes ago       Exited (0) 3 minutes ago                          reverent_torvalds
# 128ec8ceab71        hello-world           "/hello"                 4 minutes ago       Exited (0) 4 minutes ago                          exciting_chebyshev
```

### 5. How to Name or Rename a Container
By default, every container has two identifiers. They are as follows:
  - **CONTAINER ID** - a random 64 character-long string.
  - **NAME** - combination of two random words, joined with an underscore.

Naming a container can be achieved using the --name option:
```shell
docker container run --detach --publish 8888:80 --name hello-dock-container fhsinchy/hello-dock

# b1db06e400c4c5e81a93a64d30acc1bf821bed63af36cab5cdb95d25e114f5fb
```

You can even rename old containers using the container rename command. Syntax for the command is as follows:
```shell
docker container rename <container identifier> <new name>
```

### 6. How to Stop or Kill a Running Container
Containers running in the foreground can be stopped by simply closing the terminal window or hitting ctrl + c. Containers running in the background, however, can not be stopped in the same way.

There are two commands that deal with this task. The first one is the container stop command. Generic syntax for the command is as follows:
```shell
docker container stop <container identifier>
```

Where container identifier can either be the id or the name of the container.

The stop command shuts down a container gracefully by sending a SIGTERM signal. If the container doesn't stop within a certain period, a SIGKILL signal is sent which shuts down the container immediately.

In cases where you want to send a SIGKILL signal instead of a SIGTERM signal, you may use the container kill command instead. 
```shell
$ docker container kill <container identifier>
```

### 7. How to Restart a Container
When I say restart I mean two scenarios specifically. They are as follows:
- Restarting a container that has been previously stopped or killed.
- Rebooting a running container.

Stopped containers remain in your system in cache. If you want you can restart them. The container start command can be used to start any stopped or killed container. The syntax of the command is as follows:
```shell
$ docker container start <container identifier>
```
The container start command starts any container in detached mode by default and retains any port configurations made previously.

Now, in scenarios where you would like to reboot a running container you may use the container restart command. 
```shell
$ docker container restart <container identifier>
```
The main difference between the two commands is that the **container restart** command attempts to stop the target container and then starts it back up again, whereas the start command just starts an already stopped container.

In case of a stopped container, both commands are exactly the same. But in case of a running container, you must use the **container restart** command.

### 8. How to Create a Container Without Running
The container run command which is in reality a combination of two separate commands. These commands are as follows:
- **container create** command creates a container from a given image.
- **container start** command starts a container that has been already created.

"How to Run a Container" section using these two commands, you can do something like the following:
```shell
$ docker container create --publish 8080:80 fhsinchy/hello-dock

# 2e7ef5098bab92f4536eb9a372d9b99ed852a9a816c341127399f51a6d053856

$ docker container ls --all

# CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS               NAMES
# 2e7ef5098bab        fhsinchy/hello-dock   "/docker-entrypoint.…"   30 seconds ago      Created                                 hello-dock
```