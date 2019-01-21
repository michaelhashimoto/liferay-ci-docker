# Liferay CI Docker (CentOS 6)

This image includes the following:
* GNOME Desktop Environment
* Tiger VNC
* Supervisord (To manage services)

In order to run this image please run the following commands:
```
docker-compose build
docker-compose up -d
```

You can use any VNC Client to connect at this URL:
```
localhost:5901
```