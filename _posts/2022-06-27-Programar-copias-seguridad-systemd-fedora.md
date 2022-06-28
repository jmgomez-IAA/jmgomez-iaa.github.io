---
title: "Programar copias seguridad con systemd"
date: 2022-06-27
categories:
  - blog
tags:
  - systemd
  - sysadmin
  - backup
---

Systemd incorpora timers para poder automatizar tareas que deben ejecutarse pediodicamente.

# Descripcion

Vamos a craer un servicio que se ejecute una vez a la semana y que realice copias de seguridad de nuestros datos.


# Procedimiento

El proceso de crear una copia de seguridad se puede dividir en tres partes:

 - Un script que haga el backup.
 - Un servicio que se encargue de ejecutar el script que hace las copas de seguridad.
 - Un timer para ejecutar el servicio periodicamente. 

## Backup Script
El primer paso sera crear un script que se encargue de recopilar toda la informacion de la que queremos hacer la copia de seguridad, empaquetar los ficheros de nuestro sistema. Este script puede ser diferente en funcion de al aplicaccion a la que le hagamos el backup, para este ejemplo vamos a emplear un servidor de subversion.

```shell
~]$ cat /usr/local/bin/backup-svn.sh
#!/bin/bash

REPO_BASE=/home/svn/repositories
BACKUP_FOLDER=/srv/backups

#Create a backup of the svn repositories
for f in $REPO_BASE/*;
do
        REPO_NAME=${f##*/}
        BACKUP_SHORTNAME="${REPO_NAME}.dump.gz"
        BACKUP_LONGNAME="${REPO_NAME}-$(date +"%Y-%m-%d-%T").dump.gz"

        echo "Dump $REPO_NAME to $BACKUP_FOLDER/${BACKUP_LONGNAME}"
        svnadmin dump --quiet $f | gzip -9 > $BACKUP_FOLDER/${BACKUP_LONGNAME}

        # Prepare the file to synchronize
        rm $BACKUP_FOLDER/${BACKUP_SHORTNAME}
        cp $BACKUP_FOLDER/${BACKUP_LONGNAME} $BACKUP_FOLDER/${BACKUP_SHORTNAME}

        echo "SYNC ${BACKUP_LONGNAME} with SIGMANAS: backup.server.es"
        rsync -avz -vv --delete --quiet -e "ssh -i /home/backups/.ssh/id_rsa" ${BACKUP_FOLDER}/${BACKUP_SHORTNAME} workpc@backup.server.es:/mnt/storage/users-backups/repositories/

done
```

Este script lo vamos a colocar en /usr/local/bin.


## Crear servicio y timer en systemd

Ahora tenemos que crear en servicio y el timer en systemd para que este script se ejecute periodicamente. En systemd los servicios y los timers se crean mediante __unit files__ con los que indicamos a systemd que debe hacer.

```shell
~]# cat daily-backup.timer
[Unit]
Description=Backup the Gitea service.
RefuseManualStart=no
RefuseManualStop=no

[Timer]
Persistent=false
OnCalendar=Mon-Fri *-*-* 23:45:00
Unit=daily-backup.service

[Install]
WantedBy=default.target
```

Y el servicio que se encargue de ejecutar el script o los scripts de backup.

```shell
~]# cat daily-backup.service
[Unit]
Description=Daily Repositories Backup
RefuseManualStart=no
RefuseManualStop=no

[Service]
Type=oneshot
ExecStart=/usr/local/bin/backup-svn.sh
```

Finalmente instalamos estos servicios y timer en systemd.

# Instalamos los servicios y los timers.

Los ficheros de unidad para systemd se almacenan en /etc/systemd/ en nuestro caso, al ser un servicio para el sistema lo vamos a colocar en la carpeta system.

```shell
~]# cp daily-backup.service /etc/systemd/system/
~]# cp daily-backup.timer /etc/systemd/system/
```

Tenemos que decir a systemd que refresque la lista de ficheros de unidad para que detecte los nuestros.

```shell
~]# systemctl daemon-reload
```

Comprobamos que esten cargados los ficheros de unidad.

```shell
~]# systemctl status daily-backup.service
○ daily-backup.service - Daily Repositories Backup
     Loaded: loaded (/etc/systemd/system/daily-backup.service; static)
     Active: inactive (dead)

~]# systemctl status daily-backup.timer
○ daily-backup.timer - Timer for the backup service.
     Loaded: loaded (/etc/systemd/system/daily-backup.timer; disabled; vendor preset: disabled)
     Active: inactive (dead)
    Trigger: n/a
   Triggers: ● daily-backup.service

```

Finalmente, activamos nuestro timer para que ejecute el servicio cuando corresponda.

```shell
~]# systemctl enable daily-backup.timer
Created symlink /etc/systemd/system/default.target.wants/daily-backup.timer → /etc/systemd/system/daily-backup.timer.
~]# systemctl start daily-backup.timer
~]# systemctl status daily-backup.timer
● daily-backup.timer - Timer for the backup service.
     Loaded: loaded (/etc/systemd/system/daily-backup.timer; enabled; vendor preset: disabled)
     Active: active (waiting) since Tue 2022-06-28 12:24:21 CEST; 2s ago
    Trigger: Tue 2022-06-28 23:45:00 CEST; 11h left
   Triggers: ● daily-backup.service

jun 28 12:24:21 udit7 systemd[1]: Started Timer for the backup service..
```

Vamos a listar todos los timers que tiene systemd y comprobar que nuestro daily-backup.timer esta correcatmente configurado. 

```shell
~]#  systemctl list-timers
NEXT                         LEFT          LAST                         PASSED        UNIT                         ACTIVATES
Tue 2022-06-28 12:28:00 CEST 2min 4s left  Tue 2022-06-28 11:58:07 CEST 27min ago     pmie_check.timer             pmie_check.service
Tue 2022-06-28 12:28:10 CEST 2min 14s left Tue 2022-06-28 11:58:47 CEST 27min ago     pmie_farm_check.timer        pmie_farm_check.service
Tue 2022-06-28 12:55:00 CEST 29min left    Tue 2022-06-28 12:25:47 CEST 7s ago        pmlogger_check.timer         pmlogger_check.service
Tue 2022-06-28 12:55:10 CEST 29min left    Tue 2022-06-28 12:25:47 CEST 7s ago        pmlogger_farm_check.timer    pmlogger_farm_check.service
Tue 2022-06-28 13:03:13 CEST 37min left    Tue 2022-06-28 11:40:32 CEST 45min ago     dnf-makecache.timer          dnf-makecache.service
Tue 2022-06-28 21:28:47 CEST 9h left       Mon 2022-06-27 21:28:47 CEST 14h ago       systemd-tmpfiles-clean.timer systemd-tmpfiles-clean.service
Tue 2022-06-28 23:45:00 CEST 11h left      n/a                          n/a           daily-backup.timer           daily-backup.service
Wed 2022-06-29 00:00:00 CEST 11h left      Tue 2022-06-28 00:08:47 CEST 12h ago       unbound-anchor.timer         unbound-anchor.service
Wed 2022-06-29 00:08:00 CEST 11h left      Tue 2022-06-28 00:08:47 CEST 12h ago       pmie_daily.timer             pmie_daily.service
Wed 2022-06-29 00:10:00 CEST 11h left      Tue 2022-06-28 00:10:47 CEST 12h ago       pmlogger_daily.timer         pmlogger_daily.service
Sun 2022-07-03 01:00:00 CEST 4 days left   Sun 2022-06-26 01:25:47 CEST 2 days ago    raid-check.timer             raid-check.service
Mon 2022-07-04 00:22:37 CEST 5 days left   Mon 2022-06-27 00:58:47 CEST 1 day 11h ago fstrim.timer                 fstrim.service

12 timers listed.
Pass --all to see loaded but inactive timers, too.
```

como vemos aun no se ha ejecutado ninguna vez.

Para ver el log de la ejecucion: ```$ journalctl -xfu service-name.service ```

# Conclusion

Ahora nuestras copias de seguridad estan automatizadas.

# Referencias

[Understanding and administering systemd](https://docs.fedoraproject.org/en-US/quick-docs/understanding-and-administering-systemd/)
[Systemd timers](https://fedoramagazine.org/systemd-timers-for-scheduling-tasks/)
[StackOverflow Folder /usr/local/bin](https://unix.stackexchange.com/questions/4186/what-is-usr-local-bin)
[Filesystem Hierarchy Standard](https://www.pathname.com/fhs/)