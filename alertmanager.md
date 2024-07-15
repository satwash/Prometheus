# Installation of AlertManager
### Download and extract AlertManager: 
```sh
wget <url>
tar xvf <alert_manager>
```
### Add "alertmanager" user:
```sh
useradd --no-create-home --shell /bin/false alertmanager
```
### Create Directories for storing alertmanager config file and data and change their ownership:
```sh
mkdir -p /etc/alertmanager/templates
mkdir /var/lib/alertmanager
chown alertmanager:alertmanager /etc/alertmanager
chown alertmanager:alertmanager /var/lib/alertmanager

```
### Move the downloaded Prometheus AlertManager binary:
```sh
mv alertmanager-0.21.0.linux-amd64 alertmanager-files
```
### Copy the "alertmanager" and "amtools" from alertmanager-files to the above created directory and change the ownership:
```sh
cp alertmanager-files/alertmanager /usr/bin/
cp alertmanager-files/amtool /usr/bin/
chown alertmanager:alertmanager /usr/bin/alertmanager
chown alertmanager:alertmanager /usr/bin/amtool
```

### Copy the "alertmanager.yml" file and change the ownership:
```sh
cp alertmanager-files/alertmanager.yml /etc/alertmanager/alertmanager.yml
chown alertmanager:alertmanager /etc/alertmanager/alertmanager.yml
```


### Setup AlertManager Service:
```sh
vi /usr/lib/systemd/system/alertmanager.service
```
### alertmanager.service contents:
```sh
[Unit]
Description=AlertManager
Wants=network-online.target
After=network-online.target

[Service]
User=alertmanager
Group=alertmanager
Type=simple
ExecStart=/usr/bin/alertmanager \
    --config.file /etc/alertmanager/alertmanager.yml \
    --storage.path /var/lib/alertmanager/

[Install]
WantedBy=multi-user.target
```
### Update permissions:
```sh
chmod 664 /usr/lib/systemd/system/alertmanager.service
```

### Restart daemon and start the "alertmanager.service":
```sh
systemctl daemon-reload
systemctl start alertmanager
systemctl enable alertmanager
systemctl status alertmanager
```
  
## Update the prometheus.yml with alermanager config
### Update the Prometheus configuration file i.e /etc/prometheus/prometheus.yml:
```sh
vi /etc/prometheus/prometheus.yml
```
### add the following rule:
```sh
alerting:
  alertmanagers:
    - static_configs:
        - targets:
           - localhost:9093
```
### URestart prometheus service:
```sh
systemctl restart prometheus
```

