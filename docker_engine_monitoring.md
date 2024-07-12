## Docker Engine monitoring(Docker Host monitoring)
### Create/edit /etc/docker/daemon.json file:
```sh
vi /etc/docker/daemon.json
```
### Add below given lines in it:
```sh
{
  "metrics-addr" : "127.0.0.1:9323",
  "experimental" : true
}
```
### Restart docker service:
```sh
systemctl restart docker
```
### Verify if docker is exporting the metrics:
```sh
curl localhost:9323/metrics
```
### Edit "/etc/prometheus/prometheus.yml" file on Prometheus server:
```sh
vi /etc/prometheus/prometheus.yml
```
### Add below given lines under "scrape_configs":
```sh
  - job_name: "docker"
    static_configs:
      - targets: ["<Docker_host>:9323"]
```
### Restart prometheus service:
```sh
systemctl restart prometheus
```

