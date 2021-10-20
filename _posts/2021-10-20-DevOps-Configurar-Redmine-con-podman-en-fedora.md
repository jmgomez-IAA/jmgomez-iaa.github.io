---
title: "-DevOps Configurar Redmine con podman en fedora"
date: 2021-10-20
categories:
  - blog
tags:
  - redmine
  - podman
  - devops
---

# Descripcion

# Procedimiento

]# podman network create redmine-net
]# podman pod create -p 3100:3000 -p 3122:22 --net redmine-net  --name
redmine-pod
]# mkdir postgres
]# podman run -d -e POSTGRES_PASSWORD=testpass -e POSTGRES_DB=redmine
-e POSTGRES_USER=redmine -v ./postgres:/var/lib/postgresql/data:z
--pod redmine-pod postgres:latest
]#   podman run -d --pod redmine-pod -e REDMINE_DB_POSTGRES=localhost
-e REDMINE_DB_USERNAME=redmine -e REDMINE_DB_PASSWORD=testpass redmine
]#   podman logs -l

udit76.iaa.es:3100

user: admin
pass: Gl

# Referencias

[Run PostgreSQL + PGAdmin in pods using podman](https://dev.to/pr0pm/run-postgresql-pgadmin-in-pods-using-podman-386o)