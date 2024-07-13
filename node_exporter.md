## Node exporter configuration:
###  Node Exporter exposes metrics at http://localhost:9100/metrics. (similar to agent)

###  Download and extract "Node Exporter":
```sh
wget <Node_exporter_url>
tar xvf <node_exporter>
```

###  Add "node_exporter" user to run the service:
```sh
sudo useradd --no-create-home --shell /bin/false node_exporter
```

###  Copy the binaries:
```sh
sudo cp node_exporter /usr/local/bin
```

###  Change the ownership of binaries:
```sh
sudo chown node_exporter:node_exporter /usr/local/bin/node_exporter
```

###  Create systemd file for the service:
```sh
vi /etc/systemd/system/node_exporter.service
```
### pushgateway.service contents:
```sh
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter

[Install]
WantedBy=multi-user.target
```
### Restart daemon and start the node_exporter service:
```sh
systemctl daemon-reload
systemctl start node_exporter
systemctl enable node_exporter
systemctl status node_exporter
```

## Configure Prometheus scrape config to add the node:
### Add below lines under scrape_configs:
```sh
scrape_configs:
    - job_name: "prometheus"
    static_configs:
        - targets: ["localhost:9090"]
```
### restart prometheus:
```sh
sudo systemctl restart prometheus
```