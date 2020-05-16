---
title: "Instalacion de redmine en RPi3"
date: 2018-06-05
categories:
  - blog
tags:
  - rpi3
  - redmine
  - sysadmin
---

[Redmine](http://www.redmine.org) es una herramienta para la gestión de proyectos, que con sus diversas funcionalidades permite a los usuarios de diferentes proyectos realizar el seguimiento y organización de los mismos. Además es posible optimizar su funcionamiento agregando funcionalidades. Incluye un sistema de seguimiento de incidentes con seguimiento de errores. Otras herramientas que incluye son calendario de actividades, diagramas de Gantt para la representación visual de la línea del tiempo de los proyectos, wiki, foro, visor del repositorio de control de versiones, RSS, control de flujo de trabajo basado en roles, integración con correo electrónico, entre otras opciones.

Está escrito usando el framework Ruby on Rails. Es software libre y de código abierto, disponible bajo la Licencia Pública General de GNU v2.

En resumen, podemos decir que entre las principales caracteristicas de Redmine podemos destacar:
* Diseñados a medida.
* Costes muy ajustados.
* Recursos limitados.
 * Cantidad de memoria.
 * Potencia de cálculo.
 * Perifericos disponibles.
 * Puertos de E/S.

## Instalacion

```shell
~]$ sudo apt-get install install apache2 mysql-server
~]$ sudo apt-get install redmine redmine-mysql
..
Setting up ruby-rails (2:4.2.7.1-1) ...
Setting up redmine (3.3.1-4) ...
....

```

A continuación se nos pide,que elijamos si queremos hacer la congifuración a mano, que dejar el proceso automatico. Let's it go!! 

![Imagen del instalador, indicando que tipo de instalacion: por defecto o manual](images/rpi3_redmine_install_001.png)

Cuando nos solicite el tipo de base de datos, seleccionamos MySQL y despues nos pedira un password por defecto para redmine, le damos uno.

![Imagen del instalador, seleccionamos MYSQL como tipo de base de datos](images/rpi3_redmine_install_002.png)

Instalamos libpassenger
`~]$ sudo apt-get install libapache2-mod-passenger`

### Prueba de la instalación
En primer lugar, vamos a comprobar que el usuario para la base de datos y la base de datos (con sus tablas) se han creado con exito.

Nos conectamos a nuestra MariaDB, es muy posible que no conozcas el password del usuario root (como es mi caso), asi que nos vamos a conectar empleando `sudo`:
```shell
~]$ sudo mysql --user=root 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 18
Server version: 10.1.23-MariaDB-9+deb9u1 Raspbian 9.0

MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| redmine_default    |
+--------------------+

MariaDB [(none)]> select User from mysql.user;
+------------------+
| User             |
+------------------+
| redmine/instance |
| root             |
+------------------+
2 rows in set (0.00 sec)

MariaDB [(none)]> show tables from redmine_default
    -> ;
+-------------------------------------+
| Tables_in_redmine_default           |
+-------------------------------------+
| attachments                         |
| auth_sources                        |
| boards                              |
| changes                             |
| changeset_parents                   |
| changesets                          |
| changesets_issues                   |
| comments                            |
....
...
..
| wiki_contents                       |
| wiki_pages                          |
| wiki_redirects                      |
| wikis                               |
| workflows                           |
+-------------------------------------+
55 rows in set (0.00 sec)

```

Como vemos, la instalación de redmine nos ha creado el sistema de base de datos __redmine_default__ y el usuario __redmine/instance__. Además ha creado un conjunto de tablas para que redmine las use.

La instalacion de Redmine incluye un fichero en el que se almacenan, las credenciales de acceso a las bases de datos, es el fichero database.yml dentro de la carpeta /usr/share/redmine/config.

```shell
~]$ sudo cat config/database.yml 
production:
  adapter: mysql2
  database: redmine_default
  host: localhost
  port: 3306
  username: redmine/instance
  password: ********
  encoding: utf8
```

Finalmente, vamos a verificar que la instalación del entorno web es correcta; para ello empleamos bundler. Redmine proporciona un entorno de prueba para probar el entorno. 

En primer lugar nos instalamos bundler:

install bundler:
```shell
~]$ sudo gem install bundler
```

Esta parte me la he saltado, pero todo parece ir bien!!
```shell
bundle install --without development test
generate secret token:
bundle exec rake generate_secret_token
prepare DB and install all tables:

RAILS_ENV=production bundle exec rake db:migrate
RAILS_ENV=production bundle exec rake redmine:load_default_data
```

Lanzamos el webbrick de bundler y verficamos nuestra instalación.
```shell
~]$ sudo bundle exec ruby bin/rails server -b ip_de_nuestro_equipo -e production
=> Booting WEBrick
=> Rails 4.2.7.1 application starting in production on http://10.9.0.104:3000
=> Run `rails server -h` for more startup options
=> Ctrl-C to shutdown server
[2018-06-05 15:02:56] INFO  WEBrick 1.3.1
[2018-06-05 15:02:56] INFO  ruby 2.3.3 (2016-11-21) [arm-linux-gnueabihf]
[2018-06-05 15:02:56] INFO  WEBrick::HTTPServer#start: pid=19643 port=3000
```

mientras dejamos el webbrick ejecutandose en la consola, abrimos el navegador y comprobamos que redmine es visible accediendo a la http://ip_de_nuestro_equipo:3000 que es el puerto por defecto:
![Acesso desde el navegador a servideo de webbrick, en 10.9.0.104 y puero 3000](images/rpi3-redmine-install-003.png)

Hacemos Ctrl+C para cerrar y continuamos con nuestro proceso:

### Integración de redmine y Apache
Finalmente hacemos publica nuestra instalacion de Redmine. Apache como como usuario www-data y por lo tanto debemos darle permisos sobre algunos ficheros de Redmine.

```shell
sudo chown -R www-data files log tmp public/plugin_assets
sudo chmod -R 755 files log tmp public/plugin_assets
sudo chown www-data:www-data Gemfile.lock
```

```shell
~]$ sudo ln -s /usr/share/redmine/public /var/www/html/redmine
~]$ sudo chown -R www-data:www-data /var/www/html/redmine

~]$  sudo touch /etc/apache2/sites-available/redmine.conf
~]$  sudo nano /etc/apache2/sites-available/redmine.conf
~]$  sudo a2dissite 000-default.conf
~]$  sudo a2ensite redmine
Enabling site redmine.
To activate the new configuration, you need to run:
  systemctl reload apache2
```

Para evitar errores con los permisos en passenger, lo hacemos que emplee el usuario www-data, es decir, añadimos `PassengerUser www-data` al fichero **/etc/apache2/mods-available/passenger.conf**.
  
 To avoid permission error the passenger mod needs to run also as www-data.
```shell
~]$ nano  /etc/apache2/mods-available/passenger.conf
<IfModule mod_passenger.c>
  PassengerRoot /usr/lib/ruby/vendor_ruby/phusion_passenger/locations.ini
  PassengerDefaultRuby /usr/bin/ruby
  PassengerUser www-data
</IfModule>
```

Ahora finalmente reiniciamos el servicio de apache2.
```shell
~]$ sudo systemctl reload apache2

~]$ systemctl status apache2
● apache2.service - The Apache HTTP Server
   Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2018-06-05 15:23:41 UTC; 6s ago
  Process: 19846 ExecStop=/usr/sbin/apachectl stop (code=exited, status=0/SUCCESS)
  Process: 19439 ExecReload=/usr/sbin/apachectl graceful (code=exited, status=0/SUCCESS)
  Process: 19854 ExecStart=/usr/sbin/apachectl start (code=exited, status=0/SUCCESS)
 Main PID: 15639 (code=exited, status=0/SUCCESS)
   CGroup: /system.slice/apache2.service
           ├─19890 /usr/sbin/apache2 -k start
           ├─19892 Passenger watchdog
           ├─19899 Passenger core
           ├─19913 Passenger ust-router
           ├─19928 /usr/sbin/apache2 -k start
           └─19929 /usr/sbin/apache2 -k start


```

Ahora ya podemos ir a nuestro navegador y visitar la web, http://reemplazar_por_nuestra_ip/redmine.


## Referencias
http://www.redmine.org/projects/redmine/wiki/RedmineInstall 
http://www.tylerforsythe.com/2015/04/redmine-version-3-onto-raspberry-pi-2/ 
https://github.com/danmunn/redmine_dmsf
