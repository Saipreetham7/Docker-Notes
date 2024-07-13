# Docker-Notes

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

