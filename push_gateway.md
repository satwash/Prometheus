## Push gateway config
###  It is used for short-lived batch jobs.
###  jobs push metrics to the Pushgateway, which stores them until Prometheus scrapes the Pushgateway.

###  Download and extract push gateway:
```sh
wget <push_gateway_url>
tar xvf <push_gateway>
```

###  Add "pushgateway" user to run the service:
```sh
sudo useradd --no-create-home --shell /bin/false pushgateway
```

###  Copy the binaries:
```sh
sudo cp pushgateway /usr/local/bin
```

###  Change the ownership of binaries:
```sh
chown pushgateway:pushgateway /usr/local/bin/pushgateway
```

###  Create systemd file for the service
```sh
vi /etc/systemd/system/pushgateway.service
```
### pushgateway.service contents:
```sh
[Unit]
Description=Prometheus Pushgateway
Wants=network-online.target
After=network-online.target
[Service]
User=pushgateway
Group=pushgateway
Type=simple
ExecStart=/usr/local/bin/pushgateway
[Install]
WantedBy=multi-user.target
```
### Restart daemon and start the Prometheus:
```sh
systemctl daemon-reload
systemctl start prometheus
systemctl enable prometheus
systemctl status prometheus
```