# pi
This repository contains documentation and setup for my Raspberry Pi. 

## Prerequisites
Instructions below assume a headless setup.

- You have a Raspberry Pi, power supply/cable, and SD card.
- You have a separate computer for writing to the SD card and SSH-ing to the Pi.

## Initial Setup

1. Plug the SD card into a computer, install [Raspberry Pi Lite](https://www.raspberrypi.com/software/) on it via the Raspberry Pi Imager. Be sure to configure your hostname, SSH, and wifi settings prior to installation.
1.  Put the SD card into the Pi and plug it in.
1. On another computer, `ping <hostname>.local` to find the IP address.
1. `ssh <username>@<ip_address>` or `ssh <username>@<hostname>.local`
1. Enter password set in the imager settings .

**Now you should be connected to the Pi** :grin:

## Apps and packages

### Update packages

```
sudo apt update
sudo apt upgrade -y
```

### Install useful packages

```
sudo apt install \
  vim \
  git
```

### Swtich to zsh shell and use oh my zsh
Install zsh
```
sudo apt install zsh
```

Set it as your default shell
```
chsh -s /bin/zsh
```

Install oh my zsh
```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

Install the plugins [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions) and [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting/tree/master). Add `git` and `history` to the plugins list in `~/.zshrc`.

### Docker
[Docker](https://www.docker.com) is a software platform for building applications based on containers. It is used to run all the applications on my Pi.

Install Docker and set your user permissions. You will be logged out and need to re-login.

```
curl -sSL https://get.docker.com | sh
sudo usermod -aG docker $USER
logout
```

After re-logging in, enable the Docker system service. This ensures Docker automatically starts whenever you reboot your system and will also start containers configured with a restart-policy of always or unless-stopped. 

```
sudo systemctl enable docker
```

### Portainer
[Portainer](https://github.com/portainer/portainer) is a lightweight management UI which allows you to easily manage your different Docker environments.
Pull the Portainer image and run it.

```
docker pull portainer/portainer-ce:latest
docker run -d -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```

Access the Portainer UI via `http://<ip_address>:9000`.

### Homebridge
[Homebridge](https://github.com/homebridge/homebridge) is a lightweight NodeJS server you can run on your home network that emulates the iOS HomeKit API.

Update the `HOMEBRIDGE_CONFIG_UI_PORT` env var in the [homebridge/docker-compose.yml](https://github.com/padabap/pi/blob/main/homebridge/docker-compose.yml) to the desired port. Then start up homebridge:

```
cd ~/pi/homebridge && docker compose up -d
```

Access the homebridge UI via `http://<ip_address>:<port_number>`. The default port number is 8581.

### Pi-hole
[Pi-hole](https://github.com/pi-hole/pi-hole) is a DNS sinkhole the blocks network-level advertisements and Internet trackers.

Update the ports and password as desired in [pihole/docker-compose.yml](https://github.com/padabap/pi/blob/main/pihole/docker-compose.yml). Then start up pi-hole:

```
cd ~/pi/pihole && docker compose up -d
```

Access the pi-hole UI at `http://<ip_address>/admin/index.php`.

### Prometheus & Grafana
[Prometheus](https://prometheus.io) + [Node Exporter](https://github.com/prometheus/node_exporter?tab=readme-ov-file) is a data store for hardware and kernal metrics. With [Grafana](https://grafana.com) as a data visualizer, you can get a nice dashboard of your system metrics.

Update the username and password environment variables in [prometheus-grafana/docker-compose.yml](https://github.com/padabap/pi/blob/main/prometheus-grafana/docker-compose.yml) as desired:
- `GF_SECURITY_ADMIN_USER`
- `GF_SECURITY_ADMIN_PASSWORD`

Start everything up:
```
cd ~/pi/prometheus-grafana && docker compose up -d
```

Verify all 3 services are running, it may take a few minutes to load:
- See metrics via Node Exporter at `http://<ip_address>:9100/metrics`
- Prometheus should now be running at `http://<ip_address>:9090`
- Grafana should now be running at `http://<ip_address>:3000`

You can create a dashboard on the Grafana webpage once all 3 services are running. Log in via the username/password set in the docker-compose fil (default username = "admin", password = "grafana"). Then create a new dashboard - [Node Exporter Full](https://grafana.com/grafana/dashboards/1860-node-exporter-full/) is the template I use. Import the dashboard template via its ID and select your Prometheus datasource to visualize your metrics.