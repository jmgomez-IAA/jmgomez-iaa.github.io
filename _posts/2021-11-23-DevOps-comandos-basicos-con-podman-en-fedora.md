---
title: "DevOps comandos basicos con podman en fedora"
date: 2021-11-23
categories:
  - blog
tags:
  - commands
  - podman
  - devops
---

Podman 

# Descripcion
In this quick example, im trying to show you how easy is to have a Fedora latest running container for playing around with it. I won’t cover installation, use your package manager (i.e. dnf/yum install podman, etc).

# Procedimiento

Creamos nuestra red y nuestro pod.
```shell
~]# podman run -it --name fedora fedora  /bin/bash

```

After this quick command, and i believe simple, podman has downloaded the latest fedora image from docker.io, created a running container, gave it a name for easier handling and started an interactive bash shell, the command that we specified at the end of it.

-it flags meaning, -i interactive, keep stdin open, -t allocatte a pseudo tty.

```shell
$ podman ps -a
CONTAINER ID  IMAGE                            COMMAND    CREATED        STATUS                   PORTS  NAMES
45a22b919c21  docker.io/library/fedora:latest  /bin/bash  9 minutes ago  Exited (0) 1 second ago         fedora

```


# Rerencias

[Ch.1 Finding, Running, and Building Containers with podman, skopeo, and buildah](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux_atomic_host/7/html/managing_containers/finding_running_and_building_containers_with_podman_skopeo_and_buildah)

kernel-devel kernel-headers

podman run -it --volume ./files:/mnt:z fedora /bin/bash

1. Introduction

In this tutorial, we'll look at Podman (short for “Pod Manager”), its features and usage.
2. Podman

Podman is an open-source container management tool for developing, managing and running OCI containers. Let's take a look at some of the advantages of Podman, in comparison with other container management tools:

    Images created by Podman are compatible with other container management tools. The images created by Podman adhere to OCI standard, and hence they can be pushed to other container registries like Docker Hub
    It can be run as a normal user without requiring root privileges. When running as a non-root user, Podman creates a user namespace inside which it acquires the root permission. This allows it to mount file systems and setup required containers
    It provides the ability to manage pods. Unlike the other container runtime tools, Podman lets the user manage pods (a group of one or more containers that operate together). Users can perform operations like create, list, inspect on the pods

However, there are certain limitations to Podman:

    It only runs on Linux based systems. Currently, Podman only runs on Linux-based operating systems and doesn't have a wrapper for Windows and macOS.
    There is no alternative for Docker Compose. Podman doesn't have support for managing multiple containers locally, similar to what Docker Compose does. An implementation of Docker Compose using the Podman backend is being developed as part of the podman-compose project, but this is still work in progress.

3. Comparison to Docker

Now that we have understood what Podman is and what's its advantages and limitations are, let's compare it with Docker, one of the most widely used container management tools.
3.1. Command Line Interface (CLI)

Podman offers the same set of commands exposed by the Docker client. In other words, there is a one-to-one mapping between the commands of these two utilities.

However, the commands like podman ps and podman images will not show the containers or images created using Docker. This is because Podman's local repository is /var/lib/containers as opposed to /var/lib/docker maintained by Docker.
3.2. Container Model

Docker uses a client-server architecture for the containers, whereas Podman uses the traditional fork-exec model common across Linux processes. The containers created using Podman, are the child process of the parent Podman process. This is the reason that when the version command is run for both Docker and Podman, Docker lists the versions of both client and server whereas Podman lists only it's version.

Sample output for docker version:

Client:
 Version:       17.12.0-ce
 API version:   1.35
 Go version:    go1.9.2
 Git commit:    c97c6d6
 Built: Wed Dec 27 20:11:19 2017
 OS/Arch:       linux/amd64

Server:
 Engine:
  Version:      17.12.0-ce
  API version:  1.35 (minimum version 1.12)
  Go version:   go1.9.2
  Git commit:   c97c6d6
  Built:        Wed Dec 27 20:09:53 2017
  OS/Arch:      linux/amd64
  Experimental: false

Sample output for podman version:

Version:       0.3.2-dev
Go Version:    go1.9.4
Git Commit:    "4f4a78abb40fa0e8407e8a55d5a67a2650d8fd96"
Built:         Mon Mar  5 11:10:35 2018
OS/Arch:       linux/amd64

Since Podman itself runs as a process, it doesn't require any daemon processes in the background. Unlike Podman, Docker requires a daemon process, Docker daemon, to coordinate the API requests between the client and server.
freestar
3.3. Rootless Mode

As mentioned earlier, Podman doesn't require root access to run its commands. Docker, on the other hand, being dependent on the daemon process, requires root privileges or requires the user to be part of the docker group to be able to run the Docker commands without root privilege.

$ sudo usermod -aG docker $USER

4. Installation and Usage

Let's start by installing Podman. The podman info command displays Podman system information and helps check the installation status.

$ podman info

This command displays the information related to the host such as the Kernel version, swap space used and available and also the information related to Podman such as registries it has access to pull and push images to, storage driver it uses, storage location and others:

host:
  MemFree: 546578432
  MemTotal: 1040318464
  SwapFree: 4216320000
  SwapTotal: 4216320000
  arch: amd64
  cpus: 2
  hostname: base-xenial
  kernel: 4.4.0-116-generic
  os: linux
  uptime: 1m 2.64s
insecure registries:
  registries: []
registries:
  registries:
  - docker.io
  - registry.fedoraproject.org
  - registry.access.redhat.com
store:
  ContainerStore:
    number: 0
  GraphDriverName: overlay
  GraphOptions: null
  GraphRoot: /var/lib/containers/storage
  GraphStatus:
    Backing Filesystem: extfs
    Native Overlay Diff: "true"
    Supports d_type: "true"
  ImageStore:
    number: 0
  RunRoot: /var/run/containers/storage

Let's take a look at some of the basic Podman commands.
4.1. Creating an Image

First, we'll look at creating an image using Podman. Let's start by creating a Dockerfile with the following content:
freestar

FROM centos:latest
RUN yum -y install httpd
CMD ["/usr/sbin/httpd", "-D", "FOREGROUND"]
EXPOSE 80

Now let's create the image using the build command:

$ podman build .

Here we are first pulling the base image of CentOS, installing Apache on top of it and then running it as a foreground process with the port 80 exposed. We can access the Apache server by running this image and mapping the exposed port to a host port.

The build command recursively passes all the folders available in the context directory. The current working directory by default becomes the build context when no directory is specified. Hence, it is advisable not to have files and folders that aren't required for the image creation, in the context directory.
4.2. Listing Available Images

The podman images command lists all the images available. It also supports various options to filter the images.

$ podman images

This command lists all the images available in the local repository.  It contains the information on which repository the image was pulled from, the tag, its image id, created time and size.

REPOSITORY                 TAG      IMAGE ID         CREATED         SIZE
docker.io/library/centos   latest  0f3e07c0138f    2 months ago      227MB
<none>                     <none   49030e844ce7   27 seconds ago     277MB

4.3. Running Images

The run command creates a container of a given image and then runs it. Let's run the CentOS image we have created earlier

$ podman run  -p 80:80 -dit centos

This command first checks if there is a local image available for CentOS. If the image isn't present locally, it tries to pull the image from the registries that were configured. If the image isn't present in the registries, it shows an error about unable to find the image.

The above run command specifies to map the exposed 80 port of the container to the port 80 of the host and the dit flag specifies to run the container in detached and interactive mode. The id of the container created will be the output.
4.4. Deleting Images

The rmi command removes the images present in the local repository. Multiple images can be removed by providing their ids as space-separated in the input.  Specifying the -a flag removes all the images

$ podman rmi 785188cd988c

4.5. Listing the Containers

All the available containers including the ones which aren't running can be listed using the ps -a command. Similar to the images command, this can also be used with various options.

$ podman ps -a

The output for the above command lists all the containers with the information such as image it was created from, the command used to launch it, it's status, ports it's running on and the name assigned to it.

CONTAINER ID   IMAGE    COMMAND     CREATED AT                      STATUS              PORTS                                    NAMES
eed30719cd37   centos   /bin/bash   2019-12-09 02:57:37 +0000 UTC   Up 14 minutes ago   0.0.0.0:80->80/udp, 0.0.0.0:80->80/tcp   reverent_liskov

4.6. Deleting Containers

The rm command removes the containers. This command does not remove the containers in running or paused state. They need to be first stopped and then removed.

$ podman stop eed30719cd37

$ podman rm eed30719cd37

4.7. Creating Pods

The pod create command creates a pod.  The create command supports different options.

$ podman pod create

The pod create command creates a pod with an infra container by default associated with it unless explicitly set with infra flag as false.

$ podman pod create --infra = false

Infra container allows Podman to connect various containers in the pod.
4.8. Listing Pods

The pod list command displays all the available pods

$ podman pod list

The output of this command displays the information such as the pod id, its name, number of associated containers, the id of the infra container if available:

POD ID         NAME             STATUS      CREATED       # OF CONTAINERS   INFRA ID
7e0a68528aed   gallant_raman    Running    5 seconds ago        1           c6d06673c667

All the available Podman commands and their usage can be found in the official documentation.
5. Conclusion

In this tutorial, we've looked at the basics of Podman and its features, its comparison to Docker and a few of the commands available