## Prerequisite Instalation

#### Docker Installation

``` bash
sudo apt-get update
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  
sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin

```

#### Prometheus Installation

## add Prometheus user with no shell
``` bash
sudo useradd --no-create-home --shell /bin/false prometheus
```
## create Prometheus configuration libraries directories
``` bash
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
```
## give the ownership to Prometheus to its library directory
``` bash
sudo chown prometheus:prometheus /var/lib/prometheus
```
## download Prometheus instalation directory
``` bash
cd /tmp/
wget https://github.com/prometheus/prometheus/releases/download/v2.35.0/prometheus-2.35.0.linux-amd64.tar.gz
```
## extraxt files
``` bash

tar -xvf prometheus-2.35.0.linux-amd64.tar.gz
cd prometheus-2.35.0.linux-amd64
ls

```
## copy files to their quilivant configuration directory
``` bash
sudo mv console* /etc/prometheus
sudo mv prometheus.yml /etc/prometheus
sudo chown -R prometheus:prometheus /etc/prometheus

```

## copy binary files like promtool and Prometheus

``` bash

sudo mv prometheus /usr/local/bin/
sudo mv promtool /usr/local/bin/
sudo chown prometheus:prometheus /usr/local/bin/prometheus
sudo chown prometheus:prometheus /usr/local/bin/promtool
```
## finally create the service file to be able to restart the Prometheus service
``` bash
sudo vi /etc/systemd/system/prometheus.service
```
``` bash
[Unit]
Description=Prometheus
Wants=network-online.target
After=network-online.target

[Service]
User=prometheus
Group=prometheus
Type=simple
ExecStart=/usr/local/bin/prometheus \
--config.file /etc/prometheus/prometheus.yml \
--storage.tsdb.path /var/lib/prometheus/ \
--web.console.templates=/etc/prometheus/consoles \
--web.console.libraries=/etc/prometheus/console_libraries

[Install]
WantedBy=multi-user.target
```

## Finally start and enable Prometheus service

``` bash
sudo systemctl daemon-reload
sudo systemctl start prometheus
sudo systemctl status prometheus
sudo systemctl enable prometheus
```


## Note: The Prometheus listens on HTTP port 9090 by default

