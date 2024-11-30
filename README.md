# Monitoring
Setup a Server for Monitoring tools (EC2 instance - t2.medium and 20gb storage)

Install Prometheus - https://prometheus.io/download/ 
Download: wget <tar link>
Unzip: tar -xvf <file name>
Install : ./<executable file name> &                       (& makes it run in the background)

Prometheus runs on default port 9090
Access Prometheus using <Server IPaddress>:9090

After installing Grafana, Blackbox and Node exporter ,Add this Blackbox jobs into the 
prometheus.yml file 
# [After Installing Blackbox ]

# my global config
global:
  scrape_interval: 15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label job=<job_name> to any timeseries scraped from this config.
  - job_name: "prometheus"

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ["localhost:9090"]
- job_name: 'blackbox'
    metrics_path: /probe
    params:
      module: [http_2xx]  # Look for a HTTP 200 response.
    static_configs:
      - targets:
        - http://prometheus.io    # Target to probe with http.
        - <application Url>  # Target application url (your Hello world site url)
    relabel_configs:
      - source_labels: [_address_]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: _address_
        replacement: <monitoring server ip blackbox>:9115  # The blackbox exporter's real hostname:port.

#  For system level monitoring
# [After installing Node exporter]
- job_name: 'node_exporter'
    static_configs:
targets: [‘<IP address of server>:9100’]
	
	- job_name: ‘application’
	 metrics_path: ‘/prometheus’
 static_configs:
      - targets: [‘<server IPaddress:application port>’]
        
After this restart prometheus 
Pgrep prometheus
Kill <ID>
./prometheus &

Download and Install Grafana - https://grafana.com/grafana/download
After installing Start grafana using - sudo /bin/systemctl start grafana-server
Grafana runs on default port 3000
Access Grafana using <Server IPaddress>:3000
Default login details : Username- admin
Password: admin
Add Prometheus as data source in grafana



Import Black board grafana dashboard using ID in
https://grafana.com/grafana/dashboards/7587-prometheus-blackbox-exporter/


Dashboard: for Website Monitoring



Create another dashboard for System level monitoring  from 
https://grafana.com/grafana/dashboards/1860-node-exporter-full/



For Website Level Monitoring

Download and Install Blackbox exporter - https://prometheus.io/download/#blackbox_exporter
It helps to monitor website
Blackbox exporter runs default port 9115



For System Level Monitoring 

Download and install Node_exporter for Prometheus on the Server that you want to monitor 
Download: wget- https://github.com/prometheus/node_exporter/releases/download/v1.8.2/node_exporter-1.8.2.linux-amd64.tar.gz

Unzip:  tar -xvf <folder name>
Install: ./<name of executable file> &             (& makes it runs in the background)
Node exporter runs default on 9100 port
 


