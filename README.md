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


## How to Containerize a Javascript Application
### 1. How to Write the Development Dockerfile
Docker File
```shell
FROM node:lts-alpine

EXPOSE 3000

USER node

RUN mkdir -p /home/node/app

WORKDIR /home/node/app

COPY ./package.json .
RUN npm install

COPY . .

CMD [ "npm", "run", "dev" ]
```

The explanation for this code is as follows:

- The <mark>FROM</mark> instruction here sets the official Node.js image as the base, giving you all the goodness of Node.js necessary to run any JavaScript application. The <mark>lts-alpine</mark> tag indicates that you want to use the Alpine variant, long term support version of the image.

- The <mark>USER</mark> instruction sets the default user for the image to <mark>node</mark>. By default Docker runs containers as the root user. But according to Docker and Node.js Best Practices this can pose a security threat. So it's a better idea to run as a non-root user whenever possible. The node image comes with a non-root user named <mark>node</mark> which you can set as the default user using the <mark>USER</mark> instruction.

- The <mark>RUN mkdir -p /home/node/app</mark> instruction creates a directory called <mark>app</mark> inside the home directory of the <mark>node</mark> user. The home directory for any non-root user in Linux is usually <mark>/home/\<user name\></mark> by default.

- Then the <mark>WORKDIR</mark> instruction sets the default working directory to the newly created /home/node/app directory. By default the working directory of any image is the root. You don't want any unnecessary files sprayed all over your root directory, do you? Hence you change the default working directory to something more sensible like <mark>/home/node/app</mark> or whatever you like. This working directory will be applicable to any subsequent <mark>COPY</mark>, <mark>ADD</mark>, <mark>RUN</mark> and <mark>CMD</mark> instructions.

- The <mark>COPY</mark> instruction here copies the <mark>package.json</mark> file which contains information regarding all the necessary dependencies for this application. The <mark>RUN</mark> instruction executes the <mark>npm install</mark> command which is the default command for installing dependencies using a <mark>package.json</mark> file in Node.js projects. The <mark>.</mark> at the end represents the working directory.

- The second <mark>COPY</mark> instruction copies the rest of the content from the current directory (<mark>.</mark>) of the host filesystem to the working directory (<mark>.</mark>) inside the image.

- Finally, the <mark>CMD</mark> instruction here sets the default command for this image which is <mark>npm run dev</mark> written in <mark>exec</mark> form.

- The <mark>vite</mark> development server by default runs on port <mark>3000</mark> , and adding an <mark>EXPOSE</mark> command seemed like a good idea, so there you go.


Now, to build an image from this <mark>Dockerfile.dev</mark> you can execute the following command:

```shell
docker image build --file Dockerfile.dev --tag <image name>:<version> .
```
Given the filename is not <mark>Dockerfile</mark> you have to explicitly pass the filename using the <mark>--file</mark> option. A container can be run using this image by executing the following command:

```shell
docker container run \
    --rm \
    --detach \
    --publish 3000:3000 \
    --name hello-dock-dev \
    hello-dock:dev

# 21b9b1499d195d85e81f0e8bce08f43a64b63d589c5f15cbbd0b9c0cb07ae268
```

### 2. How to Work With Bind Mounts in Docker
If you've worked with any front-end JavaScript framework before, you should know that the development servers in these frameworks usually come with a hot reload feature. That is if you make a change in your code, the server will reload, automatically reflecting any changes you've made immediately.

But if you make any changes in your code right now, you'll see nothing happening to your application running in the browser. This is because you're making changes in the code that you have in your local file system but the application you're seeing in the browser resides inside the container file system.

![alt text](https://www.freecodecamp.org/news/content/images/2021/01/local-vs-container-file-system.svg)


Using bind mounts, you can easily mount one of your local file system directories inside a container. Instead of making a copy of the local file system, the bind mount can reference the local file system directly from inside the container.

This way, any changes you make to your local source code will reflect immediately inside the container,  triggering the hot reload feature of the vite development server. 

![alt text](https://www.freecodecamp.org/news/content/images/2021/01/bind-mounts.svg)

Bind mounts can be created using the <mark>--volume</mark> or <mark>-v</mark> option for the <mark>container run</mark> or <mark>container start</mark> commands. Just to remind you, the generic syntax is as follows:

```shell
--volume <local file system directory absolute path>:<container file system directory absolute path>:<read write access>
```

Start a new container by executing the following command:

```shell
docker container run \
    --rm \
    --publish 3000:3000 \
    --name hello-dock-dev \
    --volume $(pwd):/home/node/app \
    hello-dock:dev

# sh: 1: vite: not found
# npm ERR! code ELIFECYCLE
# npm ERR! syscall spawn
# npm ERR! file sh
# npm ERR! errno ENOENT
# npm ERR! hello-dock@0.0.0 dev: `vite`
# npm ERR! spawn ENOENT
# npm ERR!
# npm ERR! Failed at the hello-dock@0.0.0 dev script.
# npm ERR! This is probably not a problem with npm. There is likely additional logging output above.
# npm WARN Local package.json exists, but node_modules missing, did you mean to install?
```

As you can see, the application is not running at all now.

That's because although the usage of a volume solves the issue of hot reloads, it introduces another problem. If you have any previous experience with Node.js, you may know that the dependencies of a Node.js project live inside the <mark>node_modules</mark> directory on the project root.

Now that you're mounting the project root on your local file system as a volume inside the container, the content inside the container gets replaced along with the <mark>node_modules</mark> directory containing all the dependencies. This means that the <mark>vite</mark> package has gone missing. **This problem can be solved using an anonymous volume.**

### 3. How to Work With Anonymous Volumes in Docker
An anonymous volume is identical to a bind mount except that you don't need to specify the source directory here. The generic syntax for creating an anonymous volume is as follows:

```shell
--volume <container file system directory absolute path>:<read write access>
```

So the final command for starting the container with both volumes should be as follows:

```shell
docker container run \
    --rm \
    --detach \
    --publish 3000:3000 \
    --name hello-dock-dev \
    --volume $(pwd):/home/node/app \
    --volume /home/node/app/node_modules \
    <image name>

# 53d1cfdb3ef148eb6370e338749836160f75f076d0fbec3c2a9b059a8992de8b
```

Here, Docker will take the entire <mark>node_modules</mark> directory from inside the container and tuck it away in some other directory managed by the Docker daemon on your host file system and will mount that directory as <marrk>node_modules</mark> inside the container.

### 4. How to Perform Multi-Staged Builds in Docker
In development mode the <mark>npm run serve</mark> command starts a development server that serves the application to the user. That server not only serves the files but also provides the hot reload feature.

In production mode, the <mark>npm run build</mark> command compiles all your JavaScript code into some static **HTML, CSS, and JavaScript** files. To run these files you don't need node or any other runtime dependencies. All you need is a server like <mark>nginx</mark> for example.

To create an image where the application runs in production mode, you can take the following steps:
- Use node as the base image and build the application.
- Install nginx inside the node image and use that to serve the static files.


This approach is completely valid. But the problem is that the <mark>node</mark> image is big and most of the stuff it carries is unnecessary to serve your static files. A better approach to this scenario is as follows:
- Use node image as the base and build the application.
- Copy the files created using the node image to an nginx image.
- Create the final image based on nginx and discard all node related stuff.

This way your image only contains the files that are needed and becomes really handy.

This approach is a **multi-staged build**. To perform such a build, create a new Dockerfile inside your **hello-dock** project directory and put the following content in it:

```shell
FROM node:lts-alpine as builder

WORKDIR /app

COPY ./package.json ./
RUN npm install

COPY . .
RUN npm run build

FROM nginx:stable-alpine

EXPOSE 80

COPY --from=builder /app/dist /usr/share/nginx/html
```

As you can see the <mark>Dockerfile</mark> looks a lot like your previous ones with a few oddities. The explanation for this file is as follows:

- Line 1 starts the first stage of the build using <mark>node:lts-alpine</mark> as the base image. The <mark>as builder</mark> syntax assigns a name to this stage so that it can be referred to later on.

- From line 3 to line 9, it's standard stuff that you've seen many times before. The <mark>RUN npm run build</mark> command actually compiles the entire application and tucks it inside <mark>/app/dist</mark> directory where <mark>/app</mark> is the working directory and <mark>/dist</mark> is the default output directory for <mark>vite</mark> applications.

- Line 11 starts the second stage of the build using <mark>nginx:stable-alpine</mark> as the base image.

- The <mark>NGINX</mark> server runs on port <mark>80</mark> by default so the line <mak>EXPOSE 80</mark> is added.

- The last line is a <mark>COPY</mark> instruction. The <mark>--from=builder</mark> part indicates that you want to copy some files from the <mark>builder</mark> stage. After that it's a standard copy instruction where <mark>/app/dist</mark> is the source and <mark>/usr/share/nginx/html</mark> is the destination. The destination used here is the default site path for NGINX so any static file you put inside there will be automatically served.

As you can see, the resulting image is a nginx base image containing only the files necessary for running the application. To build this image execute the following command:
```shell
docker image build --tag hello-dock:prod .

# Step 1/9 : FROM node:lts-alpine as builder
#  ---> 72aaced1868f
# Step 2/9 : WORKDIR /app
#  ---> Running in e361c5c866dd
# Removing intermediate container e361c5c866dd
#  ---> 241b4b97b34c
# Step 3/9 : COPY ./package.json ./
#  ---> 6c594c5d2300
# Step 4/9 : RUN npm install
#  ---> Running in 6dfabf0ee9f8
# npm WARN deprecated fsevents@2.1.3: Please update to v 2.2.x
#
# > esbuild@0.8.29 postinstall /app/node_modules/esbuild
# > node install.js
#
# npm notice created a lockfile as package-lock.json. You should commit this file.
# npm WARN optional SKIPPING OPTIONAL DEPENDENCY: fsevents@~2.1.2 (node_modules/chokidar/node_modules/fsevents):
# npm WARN notsup SKIPPING OPTIONAL DEPENDENCY: Unsupported platform for fsevents@2.1.3: wanted {"os":"darwin","arch":"any"} (current: {"os":"linux","arch":"x64"})
# npm WARN hello-dock@0.0.0 No description
# npm WARN hello-dock@0.0.0 No repository field.
# npm WARN hello-dock@0.0.0 No license field.
#
# added 327 packages from 301 contributors and audited 329 packages in 35.971s
#
# 26 packages are looking for funding
#   run `npm fund` for details
#
# found 0 vulnerabilities
#
# Removing intermediate container 6dfabf0ee9f8
#  ---> 21fd1b065314
# Step 5/9 : COPY . .
#  ---> 43243f95bff7
# Step 6/9 : RUN npm run build
#  ---> Running in 4d918cf18584
#
# > hello-dock@0.0.0 build /app
# > vite build
#
# - Building production bundle...
#
# [write] dist/index.html 0.39kb, brotli: 0.15kb
# [write] dist/_assets/docker-handbook-github.3adb4865.webp 12.32kb
# [write] dist/_assets/index.eabcae90.js 42.56kb, brotli: 15.40kb
# [write] dist/_assets/style.0637ccc5.css 0.16kb, brotli: 0.10kb
# - Building production bundle...
#
# Build completed in 1.71s.
#
# Removing intermediate container 4d918cf18584
#  ---> 187fb3e82d0d
# Step 7/9 : EXPOSE 80
#  ---> Running in b3aab5cf5975
# Removing intermediate container b3aab5cf5975
#  ---> d6fcc058cfda
# Step 8/9 : FROM nginx:stable-alpine
# stable: Pulling from library/nginx
# 6ec7b7d162b2: Already exists 
# 43876acb2da3: Pull complete 
# 7a79edd1e27b: Pull complete 
# eea03077c87e: Pull complete 
# eba7631b45c5: Pull complete 
# Digest: sha256:2eea9f5d6fff078ad6cc6c961ab11b8314efd91fb8480b5d054c7057a619e0c3
# Status: Downloaded newer image for nginx:stable
#  ---> 05f64a802c26
# Step 9/9 : COPY --from=builder /app/dist /usr/share/nginx/html
#  ---> 8c6dfc34a10d
# Successfully built 8c6dfc34a10d
# Successfully tagged hello-dock:prod
```

Once the image has been built, you may run a new container by executing the following command:

```shell
docker container run \
    --rm \
    --detach \
    --name hello-dock-prod \
    --publish 8080:80 \
    hello-dock:prod

# 224aaba432bb09aca518fdd0365875895c2f5121eb668b2e7b2d5a99c019b953
```

The running application should be available on http://127.0.0.1:8080

Multi-staged builds can be very useful if you're building large applications with a lot of dependencies. If configured properly, images built in multiple stages can be very optimized and compact.