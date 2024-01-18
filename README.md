# Monitoring
### How to install Prometheus , Node Exporter , Alertmanager and Grafana.

```
sudo apt update
```

### Installation
---
```
 sudo groupadd --system prometheus
 sudo useradd -s /sbin/nologin --system -g prometheus prometheus
```
Create Directories for Prometheus.
```
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
```
Download Prometheus from official site 
```
https://prometheus.io/download/#prometheus
```
After Download , extract the content 
``` tar vxf prometheus*.tar.gz```

### Create Repository for Grafana.
```
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
```
```
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
```

### Installation of Node Exporter , Alertmanager & Grafana.
```
sudo apt install prometheus-node-exporter
sudo apt install prometheus-alertmanager
sudo apt install grafana
```
### Configuration 
---
#### Prometheus
---
Navigate Prometheus Directory.
```
cd prometheus*/
```
```
sudo mv prometheus /usr/local/bin
sudo mv promtool /usr/local/bin
sudo chown prometheus:prometheus /usr/local/bin/prometheus
sudo chown prometheus:prometheus /usr/local/bin/promtool
```
```
sudo mv consoles /etc/prometheus
sudo mv console_libraries /etc/prometheus
sudo mv prometheus.yml /etc/prometheus
```
```
sudo chown prometheus:prometheus /etc/prometheus
sudo chown -R prometheus:prometheus /etc/prometheus/consoles
sudo chown -R prometheus:prometheus /etc/prometheus/console_libraries
sudo chown -R prometheus:prometheus /var/lib/prometheus
```
```
sudo nano /etc/prometheus/prometheus.yml
```
Write this configuration in .yml file.
```
  - job_name: prometheus

    # Override the global default and scrape targets from this job every 5 seconds.
    scrape_interval: 5s
    scrape_timeout: 5s

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ['localhost:9090' , '192.168.1.18:9090']

  - job_name: node
    # If prometheus-node-exporter is installed, grab stats about the local
    # machine by default.
    static_configs:
            - targets: ['localhost:9100' , '192.168.1.18:9100' , '192.168.1.20:9100']
  - job_name: alertmanager
    static_configs:
      - targets: ['localhost:9093' ,  '192.168.1.18:9093']
```
---
#### Create Prometheus systemd service
```
sudo nano /etc/systemd/system/prometheus.service
```
Write this in service file.
```
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
---
### Reload Systemd
```
  sudo systemctl daemon-reload  
  sudo systemctl start prometheus
  sudo systemctl start prometheus.service 
  sudo systemctl start prometheus-node-exporter
  sudo systemctl start prometheus-node-exporter.service 
  sudo systemctl start prometheus-alertmanager.service 
  sudo systemctl start grafana-server.service 
```
---
### Check status of all services.
```
  sudo systemctl status prometheus
  sudo systemctl status prometheus.service   
  sudo systemctl status prometheus-node-exporter
  sudo systemctl status prometheus-node-exporter.service   
  sudo systemctl status prometheus-alertmanager.service   
  sudo systemctl status grafana-server.service
```
