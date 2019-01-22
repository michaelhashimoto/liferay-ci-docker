# Liferay CI Docker (CentOS 7)

This image includes the following:
* GNOME Desktop Environment
* Tiger VNC
* Systemd (To manage services)
* Git
* Ant (1.9.4)
* Java JDK (8u201 & openjdk-11+28)
* Firefox (52.0.2esr & 60.4.0esr)
* Liferay Portal (Git Repository)
* MySQL CLI Client
* PostgreSQL CLI Client

In order to run this image please run the following commands:
```
docker-compose build
docker-compose up -d
```

You can use any VNC Client to connect at this URL:
```
localhost:5900
```

This docker compose includes the following
* MariaDB 10.4.1 (mariadb:10.4.1)
* MySQL 5.5 (mysql:5.5)
* MySQL 5.6 (mysql:5.6)
* MySQL 5.7 (mysql:5.7)
* PostgreSQL 10.6 (postgres:10.6)
* PostgreSQL 11.1 (postgres:11.1)

You can connect to MariaDB in the server with the following commands:
```
mysql -uroot -proot -hcentos7_mariadb_1
```

You can connect to MySQL in the server with the following commands:
```
mysql -uroot -proot -hcentos7_mysql-5.5_1
mysql -uroot -proot -hcentos7_mysql-5.6_1
mysql -uroot -proot -hcentos7_mysql-5.7_1
```

You can connect to PostgreSQL in the server with the following commands:
```
psql -h centos7_postgresql-10_1 -U postgres
psql -h centos7_postgresql-11_1 -U postgres
```