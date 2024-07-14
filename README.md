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

# CONTAINER ID        IMAGE                COMMAND                  CREATED             STATUS                     PORTS                  NAMES
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
docker container kill <container identifier>
```

### 7. How to Restart a Container
When I say restart I mean two scenarios specifically. They are as follows:
- Restarting a container that has been previously stopped or killed.
- Rebooting a running container.

Stopped containers remain in your system in cache. If you want you can restart them. The container start command can be used to start any stopped or killed container. The syntax of the command is as follows:
```shell
docker container start <container identifier>
```
The container start command starts any container in detached mode by default and retains any port configurations made previously.

Now, in scenarios where you would like to reboot a running container you may use the container restart command. 
```shell
docker container restart <container identifier>
```
The main difference between the two commands is that the **container restart** command attempts to stop the target container and then starts it back up again, whereas the start command just starts an already stopped container.

In case of a stopped container, both commands are exactly the same. But in case of a running container, you must use the **container restart** command.

### 8. How to Create a Container Without Running
The container run command which is in reality a combination of two separate commands. These commands are as follows:
- **container create** command creates a container from a given image.
- **container start** command starts a container that has been already created.

"How to Run a Container" section using these two commands, you can do something like the following:
```shell
docker container create --publish 8080:80 <container name>

# 2e7ef5098bab92f4536eb9a372d9b99ed852a9a816c341127399f51a6d053856

$ docker container ls --all

# CONTAINER ID        IMAGE                 COMMAND                  CREATED             STATUS              PORTS               NAMES
# 2e7ef5098bab        fhsinchy/hello-dock   "/docker-entrypoint.…"   30 seconds ago      Created                                 hello-dock
```

Once the container has been created, it can be started using the container start command.

```shell
docker container start <image name>

docker container ls
```

### 9. How to Remove Dangling Containers
Containers that have been stopped or killed remain in the system. These dangling containers can take up space or can conflict with newer containers.

In order to remove a stopped container you can use the container rm command. The generic syntax is as follows:
```shell
docker container rm <container identifier>
```

To find out which containers are not running, use the container ls --all command and look for containers with Exited status.

You can also remove multiple containers at once by passing their identifiers one after another separated by spaces.

Or, instead of removing individual containers, if you want to remove all dangling containers at one go, you can use the container prune command.

There is also the --rm option for the container run  and container start commands which indicates that you want the containers removed as soon as they're stopped. 

```bash
docker container run --rm --detach --publish 8888:80 --name <identifier Name> <image name>
```
### 10. How to Run a Container in Interactive Mode

As you may have already learned from your previous experiences with computers, shells are interactive programs. An image configured to run such a program is an interactive image. These images require a special **_-it_** option to be passed in the **container run** command.

As an example, if you run a container using the **ubuntu** image by executing <mark>**docker container run ubuntu**</mark> you'll see nothing happens. But if you execute the same command with the <mark>**-it**</mark> option, you should land directly on bash inside the Ubuntu container.

```shell
docker container run --rm -it ubuntu

# root@dbb1f56b9563:/# cat /etc/os-release
# NAME="Ubuntu"
# VERSION="20.04.1 LTS (Focal Fossa)"
# ID=ubuntu
# ID_LIKE=debian
# PRETTY_NAME="Ubuntu 20.04.1 LTS"
# VERSION_ID="20.04"
# HOME_URL="https://www.ubuntu.com/"
# SUPPORT_URL="https://help.ubuntu.com/"
# BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
# PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
# VERSION_CODENAME=focal
# UBUNTU_CODENAME=focal
```

The <mark>**-it**</mark> option sets the stage for you to interact with any interactive program inside a container. This option is actually two separate options mashed together.
- The <mark>**-i**</mark> or <mark>**--interactive**</mark> option connects you to the input stream of the container, so that you can send inputs to bash.

- The <mark>**-t**</mark> or <mark>**--tty**</mark> option makes sure that you get some good formatting and a native terminal-like experience by allocating a pseudo-tty.

### 11. How to Execute Commands Inside a Container
The generic syntax for passing a command to a container that is not running is as follows:

```shell
docker container run <image name> <command>
```
### 12. How to Work With Executable Images


## Docker Image Manipulation Basics

### 1. How to Create a Docker Image

A Dockerfile is a collection of instructions that, once processed by the daemon, results in an image. 

Example, Content for the Dockerfile is as follows:
```shell
FROM ubuntu:latest

EXPOSE 80

RUN apt-get update && \
    apt-get install nginx -y && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

CMD ["nginx", "-g", "daemon off;"]
```
Images are multi-layered files and in this file, each line (known as instructions) that you've written creates a layer for your image.

- Every valid <mark>Dockerfile</mark> starts with a <mark>**FROM**</mark> instruction. This instruction sets the base image for your resultant image. By setting <mark>ubuntu:latest</mark> as the base image here, you get all the goodness of Ubuntu already available in your custom image, so you can use things like the <mark>apt-get</mark> command for easy package installation.

- The <mark>EXPOSE</mark> instruction is used to indicate the port that needs to be published. Using this instruction doesn't mean that you won't need to <mark>--publish</mark> the port. You'll still need to use the <mark>--publish</mark> option explicitly. This <mark>EXPOSE</mark> instruction works like a documentation for someone who's trying to run a container using your image. It also has some other uses that I won't be discussing here.

- The <mark>RUN</mark> instruction in a Dockerfile executes a command inside the container shell. The <mark>apt-get update && apt-get install nginx -y</mark> command checks for updated package versions and installs NGINX. The <mark>apt-get clean && rm -rf /var/lib/apt/lists/*</mark> command is used for clearing the package cache because you don't want any unnecessary baggage in your image. These two commands are simple Ubuntu stuff, nothing fancy. The <mark>RUN</mark> instructions here are written in <mark>shell</mark> form. These can also be written in <mark>exec</mark> form.

- Finally the <mark>CMD</mark> instruction sets the default command for your image. This instruction is written in <mark>exec</mark> form here comprising of three separate parts. Here, nginx refers to the <mark>NGINX</mark> executable. The <mark>-g</mark> and <mark>daemon off</mark> are options for NGINX. Running NGINX as a single process inside containers is considered a best practice hence the usage of this option. The <mark>CMD</mark> instruction can also be written in shell form.

Now that you have a valid Dockerfile you can build an image out of it:
```shell
docker image <command> <options>
```

To perform an image build, the daemon needs two very specific pieces of information. These are the name of the <mark>Dockerfile</mark> and the build context. In the command issued above:
- <mark>docker image build</mark> is the command for building the image. The daemon finds any file named <mark>Dockerfile</mark> within the context.
- The <mark>.</mark> at the end sets the context for this build. The context means the directory accessible by the daemon during the build process.

Now to run a container using this image, you can use the <mark>container run</mark> command coupled with the image ID that you received as the result of the build process.

### 2. How to Tag Docker Images
Just like containers, you can assign custom identifiers to your images instead of relying on the randomly generated ID. In case of an image, it's called tagging instead of naming. The <mark>--tag</mark> or <mark>-t<mark> option is used in such cases.

Generic syntax for the option is as follows:

```shell
--tag <image repository>:<image tag>
```

In cases where you forgot to tag an image during build time, or maybe you want to change the tag, you can use the image tag command to do that:

```shell
docker image tag <image id> <image repository>:<image tag>

## or ##

docker image tag <image repository>:<image tag> <new image repository>:<new image tag>
```

### 3. How to List and Remove Docker Images

Just like the container ls command, you can use the image ls command to list all the images in your local system:

```shell
docker image ls
```

Images listed here can be deleted using the image rm command. The generic syntax is as follows:

```shell
docker image rm <image identifier>
```

You can also use the <mark>image prune</mark> command to cleanup all un-tagged dangling images as follows:

```shell
docker image prune --force
```

The <mark>--force</mark> or <mark>-f</mark> option skips any confirmation questions. You can also use the <mark>--all</mark> or <mark>-a</mark> option to remove all cached images in your local registry.

### 4. How to Understand the Many Layers of a Docker Image

To visualize the many layers of an image, you can use the image history command.
```shell
docker image history custom-nginx:packaged
```

The image comprises of many read-only layers, each recording a new set of changes to the state triggered by certain instructions. When you start a container using an image, you get a new writable layer on top of the other layers.

This layering phenomenon that happens every time you work with Docker has been made possible by an amazing technical concept called a union file system. Here, union means union in set theory. According to Wikipedia -

It allows files and directories of separate file systems, known as branches, to be transparently overlaid, forming a single coherent file system. Contents of directories which have the same path within the merged branches will be seen together in a single merged directory, within the new, virtual filesystem.
By utilizing this concept, Docker can avoid data duplication and can use previously created layers as a cache for later builds. This results in compact, efficient images that can be used everywhere.

### 5. How to Build NGINX from Source

