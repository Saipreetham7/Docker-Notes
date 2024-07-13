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

