# pi
This repository contains documentation and setup for my personal Raspberry Pi.

## Assumptions
1. The pi is running Raspberry Pi OS Lite.
2. Docker is up and running
3. Docker compose is installed

## Homebridge
Update the `HOMEBRIDGE_CONFIG_UI_PORT` env var in the [homebridge/docker-compose.yml](https://github.com/padabap/pi/blob/main/homebridge/docker-compose.yml) to the desired port. Then start up homebridge:

```cd ~/pi/homebridge && docker compose up -d```

Access the homebridge UI via `http://<ip_address>:<port_number>`

## Pihole
Update the ports and password as desired in [pihole/docker-compose.yml](https://github.com/padabap/pi/blob/main/pihole/docker-compose.yml). Then start up pihole:

```cd ~/pi/pihole && docker compose up -d```

Access the pihole UI at `http://<ip_address>/admin/index.php`. If a password wasn't explicitly set, look in the logs to find the randomly created password

```docker logs pihole | grep random```
