---
title: "DevOps configurar SAMBA con podman en fedora"
date: 2021-12-03
categories:
  - blog
tags:
  - samba
  - podman
  - devops
---

Podman 

# Descripcion
In this quick example, im trying to show you how easy is to have a Fedora latest running container for playing around with it. I won’t cover installation, use your package manager (i.e. dnf/yum install podman, etc).

# Procedimiento

Creamos nuestra red y nuestro pod.
```shell
~]# podman run --name samba-srv -p 139:139 -p 445:445 -p 137:137/udp -p 138:138/udp -v "/media/share/data/shared:/share:Z" -e USERID=1002 -e GROUPID=64003 -d dperson/samba -n -s "shared;/share;yes;yes;yes;all;none;none;Shared files" -p -g "usershare allow guests = yes" -g "map to guest = bad user" -g "load printers = no" -g "printcap cache time = 0" -g "printing = bsd" -g "printcap name = /dev/null" -g "disable spoolss = yes"

~]# podman network create samba-net
~]# podman pod create -p 139:139 -p 445:445 -p 137:137/udp -p 138:138/udp --net samba-net  --name samba-pod

```

Ahora cremos la imagen

```shell
~]# podman run --name samba-srv -v "/srv/share/:/share:Z" -e USERID=1002 -e GROUPID=64003 -d --pod samba-pod dperson/samba -n -s "shared;/share;yes;no;yes;all;none;none;Shared files" -p -g "usershare allow guests = yes" -g "map to guest = bad user" -g "load printers = no" -g "printcap cache time = 0" -g "printing = bsd" -g "printcap name = /dev/null" -g "disable spoolss = yes"
```

# Configuracion y Test
La mayoria de los parametros se pasan al servicio SAMBA dentro del contenedor.

   container_name="samba"

This will be the name for the running container. I set a variable because I use it as the base for the service name and the service configuration file later on.

    podman run --name ${container_name}

Run a new container and name it "samba" (So far, so simple). The next set of parameters modify the environment of the container.

    -p 139:139 -p 445:445 -p 137:137/udp -p 138:138/udp

Map through the 2 TCP and 2 UDP ports needed for Samba. I don't need to do any remapping, so the internal/external numbers are the same.

    -v "/media/share/data/shared:/share:Z"

Map a volume from the host OS path "/media/share/data/shared" to be "/share" inside the container. The trailing "Z" component tells Podman to apply an selinux label to the share path indicating it's for "private" use (i.e. no other container can use it). You might alternatively want to use "z" (lowercase) here to indicate other containers can access the share. If you don't specify either "z" or "Z", you're likely to hit an selinux denial to access your share.

    -e USERID=1002 -e GROUPID=64003

The default behaviour of this container image is to run the Samba daemon as "uid=100(smbuser) gid=101(smb)". Unfortunately this doesn't match the UIDs on the host OS, so I set these two environment variables that the "/usr/bin/samba.sh" script inside the container uses to modify the "smbuser" user to match the external IDs. I did try using one of the three-ish methods Podman allows you to do UID mapping, but after several iterations and four additional arguments to the command line, the daemon would keep failing-out some time after launch with an error indicating it had his some incompatibility with later kernels (Sorry - I forget exactly what it was now). In any case, this method doesn't require any live ID-remapping and might even be a little more efficient.

    -d

Interestingly I can't find any explicit documentation for this parameter in the Podman docs. It is however a clone of the Docker "detach" option, which means the container will exit when the main process exits.

    dperson/samba

This is the name of the container image to use from the Docker Hub. It's a lightweight Samba server with a helpful and reasonably terse commandline interface.

All the following parameters are handed to the main process of the docker image itself.

    -n

Start Nmbd to advertise the shares

    -s "shared;/share;yes;yes;yes;all;none;none;Shared files"

This parameter defines the file file share I'm trying to offer (You may want several of these). The "sub-parameters" are:

    share name
    source path (Within container!)
    browseable
    read only
    guest access allowed
    list of users allowed ("all" is a special case)
    list of allowed admins ("none" is special - I don't want any)
    list of users allowed to write to an RO share (I guessed "none" would work here too)
    comment describing the share

    -p

Set ownership and permissions on the shares.

Now I continue with a series of general config parameters ("-g") that are given to the Samba daemon.

    usershare allow guests = yes - This is a second way to say I want guest access?
    map to guest = bad user - If a client presents an unknown user name, consider them a guest.

The next five parameters are here because SMB is a file and printer sharing protocol, but I don't want the printer-sharing part of it. Samba doesn't have any explicit way of turning this off, but The Internet tells me these parameters together make it care very little about printing.

    load printers = no
    printcap cache time = 0
    printing = bsd
    printcap name = /dev/null
    disable spoolss = yes

So that's all the Samba and Podman stuff out of the way. I now just have to make a Systemd service out of it.

    service="container-${container_name}"
    service_file="/etc/systemd/system/${service}.service"

These variables just set the service name and the corresponding service definition file name, based on the container name I defined before.


# Referencias
[How to run a "simple" Samba Podman container under Fedora 31](https://blog.m0les.com/2020/03/how-to-run-simple-samba-podman.html)

[dperson/samba Image](https://hub.docker.com/r/dperson/samba)


 Script breakdown

    firewall-cmd --add-service --permanent samba
    firewall-cmd --add-service samba 

These two lines tell firewalld to open the necessary ports for Samba now and after any future reboots.

 

    podman generate systemd --name "${container_name}" >"${service_file}"

This is one of Podman's cool little tricks. It generates a Systemd service definition file for a named container. So I can now start, stop and query the "container-samba" service.

    systemctl enable ${service}
    systemctl start ${service}

The "enable" statement just creates some symlinks to the service definition file I just made so that on the next boot, it can be started automatically. The "start" statement at this time doesn't do much (The container's already running), but it does mean that Systemd is in-sync with its state now.
