# Liferay CI Docker (CentOS 7)

This image includes the following:
* GNOME Desktop Environment
* Tiger VNC
* Systemd (To manage services)

In order to run this image please run the following commands:
```
docker-compose build
docker-compose up -d
```

You can use any VNC Client to connect at this URL:
```
localhost:5900
```