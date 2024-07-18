# Prometheus
This repository contains essential scripts and configurations for setting up and managing Prometheus

## Prometheus overview
![Untitled Diagram drawio (4)](https://github.com/user-attachments/assets/f4843076-f9eb-489e-8f91-f8f7e81b966d)

### Prometheus server
- Scrape manager: component within Prometheus that scrapes data from hosts
- TSDB: Responsible for scraping and storing time series data
- HTTP Server API: Provides access to the data and metadata
- Query Engine: Allows users to query the time series data using PromQL



#### Prometheus.yml:
  
```sh
global:
  scrape_interval: 15s  # Default scrape interval for all targets
  evaluation_interval: 15s  # Default evaluation interval for all rules
  scrape_timeout: 10s  # Global default scrape timeout

  # Labels to add to all scraped metrics
  external_labels:
    monitor: 'prometheus-monitor'

# Scrape configurations for various targets
scrape_configs:
  # Scraping Prometheus itself
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']

  # Scraping a node exporter (example target)
  - job_name: 'node'
    static_configs:
      - targets: ['localhost:9100']

  # Example of using file-based service discovery
  - job_name: 'file_sd_example'
    file_sd_configs:
      - files:
          - '/etc/prometheus/servers_to_monitored.yml'

  # Example of using relabeling
  - job_name: 'relabel_example'
    static_configs:
      - targets: ['localhost:9200']
    relabel_configs:
      - source_labels: [__address__]
        regex: '(.*):.*'
        target_label: instance
        replacement: '$1'

alerting:
  alertmanagers:
    - static_configs:
        - targets: ['localhost:9093']  # Alertmanager's address

rule_files:
  - 'rules.yml'  # Location of rules files


```
- Global Config: General settings such as scrape interval and evaluation interval.
- Scrape Configs: Define the targets to be scraped for metrics.
- Alerting Config: Configurations for the Alertmanager.
- Rule Files: Specifies files containing recording and alerting rules.
- Remote Write/Read Configs: Configurations for sending and receiving metrics to/from remote storage.

### Alert Manager
![image](https://github.com/user-attachments/assets/89a73ada-862e-4bda-b870-655600819020)
#### alertmanager.yaml:
```sh
global:
  # SMTP configuration for sending email notifications
  smtp_smarthost: 'smtp.example.com:587'
  smtp_from: 'alertmanager@example.com'
  smtp_auth_username: 'your_username'
  smtp_auth_password: 'your_password'
  # Configure Slack webhook for notifications
  slack_api_url: 'https://hooks.slack.com/services/T00000000/B00000000/XXXXXXXXXXXXXXXXXXXXXXXX'
  # Set the default timeout for notifications
  resolve_timeout: 5m

route:
  # Default receiver if no other routes match
  receiver: 'email-notifications'

  # Group alerts by these labels to reduce noise
  group_by: ['alertname', 'severity', 'instance']
  
  # Wait time before sending the first notification
  group_wait: 30s

  # Minimum interval between sending alert groups
  group_interval: 5m

  # Minimum interval between repeated notifications for the same alert
  repeat_interval: 1h

  # Nested routes for handling specific alert conditions
  routes:
    - match:
        severity: 'critical'  # Match alerts with severity 'critical'
      receiver: 'pagerduty'    # Send these alerts to the PagerDuty receiver
      continue: true           # Continue matching other routes
    
    - match:
        severity: 'warning'   # Match alerts with severity 'warning'
      receiver: 'slack'        # Send these alerts to the Slack receiver
      continue: true           # Continue matching other routes

    - match_re:
        alertname: 'HighCPUUsage|HighMemoryUsage'  # Match alerts by regex
      receiver: 'email-notifications'  # Send these alerts to the email receiver

receivers:
  # Receiver configuration for email notifications
  - name: 'email-notifications'
    email_configs:
      - to: 'admin@example.com'  # Email address to send notifications to

  # Receiver configuration for PagerDuty notifications
  - name: 'pagerduty'
    pagerduty_configs:
      - service_key: 'your_pagerduty_service_key'  # PagerDuty service key
  
  # Receiver configuration for Slack notifications
  - name: 'slack'
    slack_configs:
      - channel: '#alerts'  # Slack channel to send notifications to
        send_resolved: true # Send resolved notifications
        text: "Alert: {{ .CommonAnnotations.summary }} - {{ .CommonLabels.severity }}"  # Custom message

inhibit_rules:
  # Inhibit warning alerts if there are active critical alerts with the same alertname and job
  - source_match:
      severity: 'critical'  # Source alerts to match
    target_match:
      severity: 'warning'   # Target alerts to match
    equal: ['alertname', 'job']  # Labels that must be equal for inhibition to apply

```
- Handles alerts and notifications
- API: Provides endpoints for alert management and integration with other systems
- Configuration: Defines routing, grouping, inhibition, and notification settings in "alertmanager.yml"
- Routing: Determines how alerts are processed and routed to specific receivers
- Silences: Allows temporary muting of alerts based on specified criteria
- Inhibition: Silences alerts based on the presence of other alerts.



#### rules.yml
```sh
groups:
  - name: node
    interval: 15s
    rules:
      - record: node_memory_memFree_percent
        expr: 100 - (100 * node_memory_MemFree_bytes{job="node"} / node_memory_MemTotal_bytes{job="node"})
      - alert: LowMemory
        expr: node_memory_memFree_percent < 20
        for: 5m
        labels:
          severity: 'warning'
        annotations:
          summary: 'Low Memory detected on {{ $labels.instance }}'
          description: 'Memory available is less than 20% for more than 5 minutes.'

```
- Handles alerts and notifications
