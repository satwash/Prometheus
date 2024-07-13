## EC2 Service discovery 
###  It allows you to automatically discover EC2 instances and monitor them based on AWS tags and other metadata.
### Prometheus needs EC2_read_only access to scrape the data
  
###  Prometheus configuration file (prometheus.yml) to include a EC2 service discovery configuration:
```sh
scrape_configs:
  - job_name: 'ec2'
    ec2_sd_configs:
      - region: us-west-2          
        access_key: YOUR_AWS_ACCESS_KEY
        secret_key: YOUR_AWS_SECRET_KEY
```

### restart prometheus:
```sh
sudo systemctl restart prometheus
```