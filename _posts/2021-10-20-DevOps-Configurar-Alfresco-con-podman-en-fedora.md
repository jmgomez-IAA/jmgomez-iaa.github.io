---
title: "DevOps Configurar Alfresco con podman en fedora"
date: 2021-10-20
categories:
  - blog
tags:
  - alfresco
  - podman
  - devops
---

Alfresco es un software para la gestión de documentos permite controlar el contenido de la empresa con un gobierno de la información integrado y garantía de cumplimiento del RGPD. Elimina el caos de contenido se produce cuando los documentos se almacenan en varios lugares: en papel, en ordenadores portátiles y memorias USB, en el correo electrónico y en unidades de red, y en varios repositorios y sitios para compartir ficheros.

# Descripcion
 Alfresco incluye un repositorio de contenidos, un framework de portal web para administrar y usar contenido estándar en portales, una interfaz CIFS (el antiguo SMB. Un sistema de administración de contenido web, capacidad de virtualizar aplicaciones web y sitios estáticos vía Apache Tomcat, búsquedas vía el motor Apache Solr-Lucene y flujo de trabajo en jBPM. 

# Procedimiento




Alfresco CMS
==


Alfrsco Volumenes

volumes
├── data                    > DATA STORAGE (it's recommend to perform a backup of this folder)
│   ├── alf-repo-data       > Content Store for Alfresco Repository
│   ├── ldap                > [LDAP] Internal database
│   ├── ocr                 > [OCR] Temporal folder shared between Alfresco Repository and OCR
│   ├── postgres-data       > Internal storage for database
│   ├── slap.d              > [LDAP] Control folder
│   └── solr-data           > Internal storage for SOLR


Procedimiento
==

~]# podman pod create --name dms --hostname dms --publish 8080:8080,8443:8443

Create the directories for the volumes
# Postgresql database directories
~]# sudo -u jmgomez mkdir -p db-volume-data db-volume-logs
~]# sudo -u jmgomez mkdir activemq-volume-data 

# Alfresco
~]# sudo -u jmgomez mkdir  alfresco-volume-data alfresco-volume-logs share-volume-logs

# index and search engine
~]# sudo -u jmgomez mkdir solr-volume-data solr-volume-logs solr-log4j.properties
~]# sudo -u jmgomez touch solr-shared.properties

]# ls -la
total 4
drwxr-xr-x. 12 jmgomez jmgomez 4096 sep 29 11:44 .
drwxr-xr-x.  4 jmgomez jmgomez   37 sep 29 09:25 ..
drwxr-xr-x.  2 jmgomez jmgomez    6 sep 29 11:44 activemq-volume-data
drwxr-xr-x.  2 jmgomez jmgomez    6 sep 29 11:44 alfresco-volume-data
drwxr-xr-x.  2 jmgomez jmgomez    6 sep 29 11:44 alfresco-volume-logs
drwxr-xr-x.  2 jmgomez jmgomez    6 sep 29 11:43 db-volume-data
drwxr-xr-x.  2 jmgomez jmgomez    6 sep 29 11:43 db-volume-logs
drwxr-xr-x.  2 jmgomez jmgomez    6 sep 29 11:44 mkdir
drwxr-xr-x.  2 jmgomez jmgomez    6 sep 29 11:44 share-volume-logs
drwxr-xr-x.  2 jmgomez jmgomez    6 sep 29 11:44 solr-log4j.properties
-rw-r--r--.  1 jmgomez jmgomez    0 sep 29 11:44 solr-shared.properties
drwxr-xr-x.  2 jmgomez jmgomez    6 sep 29 11:44 solr-volume-data
drwxr-xr-x.  2 jmgomez jmgomez    6 sep 29 11:44 solr-volume-logs



#Create the containers
# Create the containers
~]# podman create --pod dms --name postgres -v ./db-volume-data:/var/lib/postgresql/data:Z -v ./db-volume-logs:/var/log/postgresql:Z -e POSTGRES_PASSWORD=pass1234 -e POSTGRES_USER=alfresco -e POSTGRES_DB=alfresco docker.io/library/postgres:11.7 postgres -c max_connections=300 -c logging_collector=true -c log_directory=/var/log/postgresql
~]# podman create --pod dms --name activemq -v ./activemq-volume-data:/opt/activemq/data:Z docker.io/alfresco/alfresco-activemq:5.15.8
~]# podman create --pod dms --name transform-core-aio docker.io/alfresco/alfresco-transform-core-aio:2.3.6
~]# podman create --pod dms --name solr6 -v ./solr-volume-data:/opt/alfresco-search-services/data:Z -v ./solr-volume-logs:/opt/alfresco-search-services/logs:Z -v ./solr-shared.properties:/opt/alfresco-search-services/solrhome/conf/shared.properties:Z,ro -v ./solr-log4j.properties:/opt/alfresco-search-services/logs/log4j.properties:Z,ro -e SOLR_ALFRESCO_HOST=localhost -e SOLR_ALFRESCO_PORT=7080 -e SOLR_SOLR_HOST=localhost -e SOLR_SOLR_PORT=8983 -e SOLR_CREATE_ALFRESCO_DEFAULTS=alfresco,archive -e ALFRESCO_SECURE_COMMS=none docker.io/alfresco/alfresco-search-services:2.0.1
~]# podman create --pod dms --name alfresco -v ./alfresco-volume-data:/usr/local/tomcat/alf_data:Z -v ./alfresco-volume-logs:/usr/local/tomcat/logs:Z -e JAVA_TOOL_OPTIONS=" -Dencryption.keystore.type=JCEKS -Dencryption.cipherAlgorithm=DESede/CBC/PKCS5Padding -Dencryption.keyAlgorithm=DESede -Dencryption.keystore.location=/usr/local/tomcat/shared/classes/alfresco/extension/keystore/keystore -Dmetadata-keystore.password=mp6yc0UD9e -Dmetadata-keystore.aliases=metadata -Dmetadata-keystore.metadata.password=oKIWzVdEdA -Dmetadata-keystore.metadata.algorithm=DESede" -e JAVA_OPTS=" -Ddb.driver=org.postgresql.Driver -Ddb.username=alfresco -Ddb.password=pass1234 -Ddb.url=jdbc:postgresql://localhost:5432/alfresco -Dsolr.host=localhost -Dsolr.port=8983 -Dsolr.secureComms=none -Dsolr.base.url=/solr -Dindex.subsystem.name=solr6 -Dshare.host=localhost -Dshare.port=8080 -Dalfresco.host=localhost -Dalfresco.port=7080 -Daos.baseUrlOverwrite=http://localhost:7080/alfresco/aos -Dmessaging.broker.url=\"failover:(nio://localhost:61616)?timeout=3000&jms.useCompression=true\" -Ddeployment.method=DEFAULT -DlocalTransform.core-aio.url=http://localhost:8090/ -Dalfresco-pdf-renderer.url=http://localhost:8090/ -Djodconverter.url=http://localhost:8090/ -Dimg.url=http://localhost:8090/ -Dtika.url=http://localhost:8090/ -Dtransform.misc.url=http://localhost:8090/ -Dcsrf.filter.enabled=false -Dgoogledocs.enabled=false -Dsample.site.disabled=true -XX:MinRAMPercentage=50 -XX:MaxRAMPercentage=80" docker.io/alfresco/alfresco-content-repository-community:6.2.2-RC1
~]# podman create --pod dms --name share  -v ./share-volume-logs:/usr/local/tomcat/logs:Z -e REPO_HOST=localhost -e REPO_PORT=7080 -e JAVA_OPTS=" -XX:MinRAMPercentage=50 -XX:MaxRAMPercentage=80 -Dalfresco.host=localhost -Dalfresco.port=7080 -Dalfresco.context=alfresco -Dalfresco.protocol=http" docker.io/alfresco/alfresco-share:6.2.2
  

# Mount pod filesystem to modify and configure.
~]# MOUNT_DIR=`podman mount alfresco`
~]# TOMCAT_DIR=$MOUNT_DIR/usr/local/tomcat

# This error is included to tell sysadmin to merge policies instead of replacing them.
#cat $TOMCAT_DIR/bin/catalina.sh | grep ==
#        -Djava.security.policy=="$CATALINA_BASE"/conf/catalina.policy \
#      -Djava.security.policy=="\"$CATALINA_BASE/conf/catalina.policy\"" \
#      -Djava.security.policy=="\"$CATALINA_BASE/conf/catalina.policy\"" \
~]# sed -i "s/==/=/g" $TOMCAT_DIR/bin/catalina.sh

# Change configuration files
~]# TOMCAT_SERVER_PORT=7005
~]# TOMCAT_HTTP_PORT=7080
~]# TOMCAT_SSL_PORT=7443
~]# TOMCAT_AJP_PORT=7009
~]# TOMCAT_JPDA_PORT=7000

sed -i "s/<Server\ port=\"8005\"\ shutdown=\"SHUTDOWN\">/<Server\ port=\"$TOMCAT_SERVER_PORT\"\ shutdown=\"SHUTDOWN\">/g" $TOMCAT_DIR/conf/server.xml
sed -i "s/port=\"8080\"\ protocol=\"HTTP\/1.1\"/port=\"$TOMCAT_HTTP_PORT\"\ protocol=\"HTTP\/1.1\"/g" $TOMCAT_DIR/conf/server.xml
sed -i "s/\ <Connector\ port=\"8443\"/\ <Connector port=\"$TOMCAT_SSL_PORT\"/g" $TOMCAT_DIR/conf/server.xml
sed -i "s/\ <Connector\ port=\"8009\"\ protocol=\"AJP\/1.3\"/\ <Connector\ port=\"$TOMCAT_AJP_PORT\"\ protocol=\"AJP\/1.3\"\ /g" $TOMCAT_DIR/conf/server.xml
sed -i "s/redirectPort=\"8443\"/redirectPort=\"$TOMCAT_SSL_PORT\"/g" $TOMCAT_DIR/conf/server.xml


References
==
Este procedimiento esta extraido de 
https://hub.alfresco.com/t5/alfresco-content-services-forum/alfresco-community-edition-with-podman/m-p/304670



EXTRA BORRAR:


Procedure
==
Este procedimiento esta extraido de 
https://hub.alfresco.com/t5/alfresco-content-services-forum/alfresco-community-edition-with-podman/m-p/304670



podman pod create --name dms --hostname dms --publish 8080:8080,8443:8443
podman create -d --pod dms --name postgres -v $HOME/volumes/db-volume-data:/var/lib/postgresql/data:Z -v $HOME/volumes/db-volume-logs:/var/log/postgresql:Z -e POSTGRES_PASSWORD=pass1234 -e POSTGRES_USER=alfresco -e POSTGRES_DB=alfresco docker.io/library/postgres:11.7 postgres -c max_connections=300 -c logging_collector=true -c log_directory=/var/log/postgresql
podman create -d --pod dms --name activemq -v $HOME/volumes/activemq-volume-data:/opt/activemq/data:Z docker.io/alfresco/alfresco-activemq:5.15.8
podman create -d --pod dms --name transform-core-aio docker.io/alfresco/alfresco-transform-core-aio:2.3.6
podman create -d --pod dms --name solr6 -v $HOME/volumes/solr-volume-data:/opt/alfresco-search-services/data:Z -v $HOME/volumes/solr-volume-logs:/opt/alfresco-search-services/logs:Z -v $HOME/volumes/solr-shared.properties:/opt/alfresco-search-services/solrhome/conf/shared.properties:Z,ro -v $HOME/volumes/solr-log4j.properties:/opt/alfresco-search-services/logs/log4j.properties:Z,ro -e SOLR_ALFRESCO_HOST=localhost -e SOLR_ALFRESCO_PORT=7080 -e SOLR_SOLR_HOST=localhost -e SOLR_SOLR_PORT=8983 -e SOLR_CREATE_ALFRESCO_DEFAULTS=alfresco,archive -e ALFRESCO_SECURE_COMMS=none docker.io/alfresco/alfresco-search-services:2.0.1
podman create -d --pod dms --name alfresco -v $HOME/volumes/alfresco-volume-data:/usr/local/tomcat/alf_data:Z -v $HOME/volumes/alfresco-volume-logs:/usr/local/tomcat/logs:Z -e JAVA_TOOL_OPTIONS=" -Dencryption.keystore.type=JCEKS -Dencryption.cipherAlgorithm=DESede/CBC/PKCS5Padding -Dencryption.keyAlgorithm=DESede -Dencryption.keystore.location=/usr/local/tomcat/shared/classes/alfresco/extension/keystore/keystore -Dmetadata-keystore.password=mp6yc0UD9e -Dmetadata-keystore.aliases=metadata -Dmetadata-keystore.metadata.password=oKIWzVdEdA -Dmetadata-keystore.metadata.algorithm=DESede" -e JAVA_OPTS=" -Ddb.driver=org.postgresql.Driver -Ddb.username=alfresco -Ddb.password=pass1234 -Ddb.url=jdbc:postgresql://localhost:5432/alfresco -Dsolr.host=localhost -Dsolr.port=8983 -Dsolr.secureComms=none -Dsolr.base.url=/solr -Dindex.subsystem.name=solr6 -Dshare.host=localhost -Dshare.port=8080 -Dalfresco.host=localhost -Dalfresco.port=7080 -Daos.baseUrlOverwrite=http://localhost:7080/alfresco/aos -Dmessaging.broker.url=\"failover:(nio://localhost:61616)?timeout=3000&jms.useCompression=true\" -Ddeployment.method=DEFAULT -DlocalTransform.core-aio.url=http://localhost:8090/ -Dalfresco-pdf-renderer.url=http://localhost:8090/ -Djodconverter.url=http://localhost:8090/ -Dimg.url=http://localhost:8090/ -Dtika.url=http://localhost:8090/ -Dtransform.misc.url=http://localhost:8090/ -Dcsrf.filter.enabled=false -Dgoogledocs.enabled=false -Dsample.site.disabled=true -XX:MinRAMPercentage=50 -XX:MaxRAMPercentage=80" docker.io/alfresco/alfresco-content-repository-community:6.2.2-RC1
podman create -d --pod dms --name share  -v $HOME/volumes/share-volume-logs:/usr/local/tomcat/logs:Z -e REPO_HOST=localhost -e REPO_PORT=7080 -e JAVA_OPTS=" -XX:MinRAMPercentage=50 -XX:MaxRAMPercentage=80 -Dalfresco.host=localhost -Dalfresco.port=7080 -Dalfresco.context=alfresco -Dalfresco.protocol=http" docker.io/alfresco/alfresco-share:6.2.2

podman unshare
MOUNT_DIR=`podman mount alfresco`
TOMCAT_DIR=$MOUNT_DIR/usr/local/tomcat


#https://docs.alfresco.com/content-services/community/develop/share-ext-points/share-config/
#share-config-custom.xml

cp ~/custom-model-context.xml $TOMCAT_DIR/shared/classes/alfresco/extension/
chown -R 33000 $TOMCAT_DIR

$MOUNT_DIR/usr/java/openjdk-11.0.7+10/bin/java -jar $TOMCAT_DIR/alfresco-mmt/alfresco-mmt*.jar uninstall org.alfresco.integrations.google.docs $TOMCAT_DIR/webapps/alfresco -nobackup -force
rm $TOMCAT_DIR/amps/alfresco-googledrive-repo-community-3.2.0.amp
cp $HOME/repo-amps/*.amp $TOMCAT_DIR/amps/
$MOUNT_DIR/usr/java/openjdk-11.0.7+10/bin/java -jar $TOMCAT_DIR/alfresco-mmt/alfresco-mmt*.jar install $TOMCAT_DIR/amps $TOMCAT_DIR/webapps/alfresco -directory -nobackup -force

# This error is included to tell sysadmin to merge policies instead of replacing them.
sed -i "s/==/=/g" $TOMCAT_DIR/bin/catalina.sh
TOMCAT_SERVER_PORT=7005
TOMCAT_HTTP_PORT=7080
TOMCAT_SSL_PORT=7443
TOMCAT_AJP_PORT=7009
TOMCAT_JPDA_PORT=7000
sed -i "s/<Server\ port=\"8005\"\ shutdown=\"SHUTDOWN\">/<Server\ port=\"$TOMCAT_SERVER_PORT\"\ shutdown=\"SHUTDOWN\">/g" $TOMCAT_DIR/conf/server.xml
sed -i "s/port=\"8080\"\ protocol=\"HTTP\/1.1\"/port=\"$TOMCAT_HTTP_PORT\"\ protocol=\"HTTP\/1.1\"/g" $TOMCAT_DIR/conf/server.xml
sed -i "s/\ <Connector\ port=\"8443\"/\ <Connector port=\"$TOMCAT_SSL_PORT\"/g" $TOMCAT_DIR/conf/server.xml
sed -i "s/\ <Connector\ port=\"8009\"\ protocol=\"AJP\/1.3\"/\ <Connector\ port=\"$TOMCAT_AJP_PORT\"\ protocol=\"AJP\/1.3\"\ /g" $TOMCAT_DIR/conf/server.xml
sed -i "s/redirectPort=\"8443\"/redirectPort=\"$TOMCAT_SSL_PORT\"/g" $TOMCAT_DIR/conf/server.xml
podman umount alfresco
exit



Tested
==

~]# podman pod create --name dms --hostname dms --publish 8080:8080,8443:8443

Create the directories for the volumes
# Postgresql database directories
~]# sudo -u jmgomez mkdir -p db-volume-data db-volume-logs
~]# sudo -u jmgomez mkdir activemq-volume-data 

# Alfresco
~]# sudo -u jmgomez  mkdir alfresco-volume-data alfresco-volume-logs share-volume-logs

# index and search engine
~]# sudo -u jmgomez mkdir solr-volume-data solr-volume-logs solr-log4j.properties
~]# sudo -u jmgomez touch solr-shared.properties

]# ls -la
total 4
drwxr-xr-x. 12 jmgomez jmgomez 4096 sep 29 11:44 .
drwxr-xr-x.  4 jmgomez jmgomez   37 sep 29 09:25 ..
drwxr-xr-x.  2 jmgomez jmgomez    6 sep 29 11:44 activemq-volume-data
drwxr-xr-x.  2 jmgomez jmgomez    6 sep 29 11:44 alfresco-volume-data
drwxr-xr-x.  2 jmgomez jmgomez    6 sep 29 11:44 alfresco-volume-logs
drwxr-xr-x.  2 jmgomez jmgomez    6 sep 29 11:43 db-volume-data
drwxr-xr-x.  2 jmgomez jmgomez    6 sep 29 11:43 db-volume-logs
drwxr-xr-x.  2 jmgomez jmgomez    6 sep 29 11:44 mkdir
drwxr-xr-x.  2 jmgomez jmgomez    6 sep 29 11:44 share-volume-logs
drwxr-xr-x.  2 jmgomez jmgomez    6 sep 29 11:44 solr-log4j.properties
-rw-r--r--.  1 jmgomez jmgomez    0 sep 29 11:44 solr-shared.properties
drwxr-xr-x.  2 jmgomez jmgomez    6 sep 29 11:44 solr-volume-data
drwxr-xr-x.  2 jmgomez jmgomez    6 sep 29 11:44 solr-volume-logs

# Create the containers
~]# podman create --pod dms --name postgres -v ./db-volume-data:/var/lib/postgresql/data:Z -v ./db-volume-logs:/var/log/postgresql:Z -e POSTGRES_PASSWORD=pass1234 -e POSTGRES_USER=alfresco -e POSTGRES_DB=alfresco docker.io/library/postgres:11.7 postgres -c max_connections=300 -c logging_collector=true -c log_directory=/var/log/postgresql
~]# podman create --pod dms --name activemq -v ./activemq-volume-data:/opt/activemq/data:Z docker.io/alfresco/alfresco-activemq:5.15.8
~]# podman create --pod dms --name transform-core-aio docker.io/alfresco/alfresco-transform-core-aio:2.3.6
~]# podman create --pod dms --name solr6 -v ./solr-volume-data:/opt/alfresco-search-services/data:Z -v ./solr-volume-logs:/opt/alfresco-search-services/logs:Z -v ./solr-shared.properties:/opt/alfresco-search-services/solrhome/conf/shared.properties:Z,ro -v ./solr-log4j.properties:/opt/alfresco-search-services/logs/log4j.properties:Z,ro -e SOLR_ALFRESCO_HOST=localhost -e SOLR_ALFRESCO_PORT=7080 -e SOLR_SOLR_HOST=localhost -e SOLR_SOLR_PORT=8983 -e SOLR_CREATE_ALFRESCO_DEFAULTS=alfresco,archive -e ALFRESCO_SECURE_COMMS=none docker.io/alfresco/alfresco-search-services:2.0.1
~]# podman create --pod dms --name alfresco -v ./alfresco-volume-data:/usr/local/tomcat/alf_data:Z -v ./alfresco-volume-logs:/usr/local/tomcat/logs:Z -e JAVA_TOOL_OPTIONS=" -Dencryption.keystore.type=JCEKS -Dencryption.cipherAlgorithm=DESede/CBC/PKCS5Padding -Dencryption.keyAlgorithm=DESede -Dencryption.keystore.location=/usr/local/tomcat/shared/classes/alfresco/extension/keystore/keystore -Dmetadata-keystore.password=mp6yc0UD9e -Dmetadata-keystore.aliases=metadata -Dmetadata-keystore.metadata.password=oKIWzVdEdA -Dmetadata-keystore.metadata.algorithm=DESede" -e JAVA_OPTS=" -Ddb.driver=org.postgresql.Driver -Ddb.username=alfresco -Ddb.password=pass1234 -Ddb.url=jdbc:postgresql://localhost:5432/alfresco -Dsolr.host=localhost -Dsolr.port=8983 -Dsolr.secureComms=none -Dsolr.base.url=/solr -Dindex.subsystem.name=solr6 -Dshare.host=localhost -Dshare.port=8080 -Dalfresco.host=localhost -Dalfresco.port=7080 -Daos.baseUrlOverwrite=http://localhost:7080/alfresco/aos -Dmessaging.broker.url=\"failover:(nio://localhost:61616)?timeout=3000&jms.useCompression=true\" -Ddeployment.method=DEFAULT -DlocalTransform.core-aio.url=http://localhost:8090/ -Dalfresco-pdf-renderer.url=http://localhost:8090/ -Djodconverter.url=http://localhost:8090/ -Dimg.url=http://localhost:8090/ -Dtika.url=http://localhost:8090/ -Dtransform.misc.url=http://localhost:8090/ -Dcsrf.filter.enabled=false -Dgoogledocs.enabled=false -Dsample.site.disabled=true -XX:MinRAMPercentage=50 -XX:MaxRAMPercentage=80" docker.io/alfresco/alfresco-content-repository-community:6.2.2-RC1
~]# podman create --pod dms --name share  -v ./share-volume-logs:/usr/local/tomcat/logs:Z -e REPO_HOST=localhost -e REPO_PORT=7080 -e JAVA_OPTS=" -XX:MinRAMPercentage=50 -XX:MaxRAMPercentage=80 -Dalfresco.host=localhost -Dalfresco.port=7080 -Dalfresco.context=alfresco -Dalfresco.protocol=http" docker.io/alfresco/alfresco-share:6.2.2
  

~]# podman unshare
  383  MOUNT_DIR=`podman mount alfresco`
  384  echo $MOUNT_DIR
  385  TOMCAT_DIR=$MOUNT_DIR/usr/local/tomcat
  386  echo $TOMCAT_DIR
  387  sed -i "s/==/=/g" $TOMCAT_DIR/bin/catalina.sh
  388  ls $TOMCAT_DIR
  389  ls \$TOMCAT_DIR/bin/
  390  ls $TOMCAT_DIR/bin/
  391  cat $TOMCAT_DIR/bin/catalina.sh
  392  TOMCAT_SERVER_PORT=7005
  393  TOMCAT_HTTP_PORT=7080
394  TOMCAT_SSL_PORT=7443
  395  TOMCAT_AJP_PORT=7009
  396  TOMCAT_JPDA_PORT=7000
  397  cat $TOMCAT_DIR/conf/server.xml | grep shutdown
  398  sed -i "s/<Server\ port=\"8005\"\ shutdown=\"SHUTDOWN\">/<Server\ port=\"$TOMCAT_SERVER_PORT\"\ shutdown=\"SHUTDOWN\">/g" $TOMCAT_DIR/conf/server.xml
  399  cat $TOMCAT_DIR/conf/server.xml | grep shutdown
  400  sed -i "s/port=\"8080\"\ protocol=\"HTTP\/1.1\"/port=\"$TOMCAT_HTTP_PORT\"\ protocol=\"HTTP\/1.1\"/g" $TOMCAT_DIR/conf/server.xml
  401  sed -i "s/\ <Connector\ port=\"8443\"/\ <Connector port=\"$TOMCAT_SSL_PORT\"/g" $TOMCAT_DIR/conf/server.xml
  402  sed -i "s/\ <Connector\ port=\"8009\"\ protocol=\"AJP\/1.3\"/\ <Connector\ port=\"$TOMCAT_AJP_PORT\"\ protocol=\"AJP\/1.3\"\ /g" $TOMCAT_DIR/conf/server.xml
  403  sed -i "s/redirectPort=\"8443\"/redirectPort=\"$TOMCAT_SSL_PORT\"/g" $TOMCAT_DIR/conf/server.xml
  404  podman umount alfresco
  405  podman pod start dms

  Aparece un problema de permisos, xq psql no puede acceder a los logs, aqui hablan del tema:
  https://github.com/Alfresco/alfresco-docker-installer/issues/1 aun que el mismo foro del procedimiento ya
  dice que la solución es dar permisos:
  , it runs under userid (33000) that doesn't own tomcat directory so you have to fix this (chown -R 33000 $TOMCAT_DIR). 