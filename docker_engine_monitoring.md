## Docker Engine monitoring
  
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
