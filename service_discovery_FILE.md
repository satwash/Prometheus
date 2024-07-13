## FILE based Service discovery 
###  It allows you to specify static targets for monitoring by reading target information from json or yaml files.

###  Prometheus configuration file (prometheus.yml) to include a file-based service discovery configuration:
```sh
scrape_configs:
  - job_name: 'file_sd'
    file_sd_configs:
      - files:
        - /path/targets.json  # For JSON file
        - '*.json'
        - /path/targets.yml   # For YAML file
        - '*.yml'
```

### targets.json:
```sh
[
  {
    "targets": ["localhost:9090", "localhost:9100"],
    "labels": {
      "job": "prometheus"
    }
  }
]

```

### targets.yml:
```sh
- targets:
  - "localhost:9090"
  - "localhost:9100"
  labels:
    job: "prometheus"
```

### restart prometheus:
```sh
sudo systemctl restart prometheus
```