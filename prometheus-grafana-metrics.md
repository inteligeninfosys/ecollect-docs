### Configure Prometheus and Grafana in Dockers
**Prometheus** is an open-source software application used for event monitoring and alerting. It records real-time metrics in a time series database with flexible queries and real-time alerting. A simple user interface where you can visualize, query, and monitor all the metrics.

**Grafana** is an open-source platform for data visualization, monitoring, and analysis. In Grafana, users can create dashboards with panels, each representing specific metrics over a set time-frame. Grafana supports graph, table, heatmap, and free text panels as well as integration with official and community-built plugins and apps that could be visualized too.

### Configure Prometheus in Docker

```
$ docker pull prom/prometheus
```
Configure Prometheus endpoint
```
# prometheus.yml
# my global config
global:
  scrape_interval:     15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: 'prometheus'
    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.
    static_configs:
    - targets: ['127.0.0.1:9090']

  - job_name: 'loopback-apis'
    metrics_path: '/metrics'
    scrape_interval: 5s
    static_configs:
    - targets: ['127.0.0.1:8000']
```
#### Run the Prometheus docker container in the background
```
$ docker run -d --name prometheus -p 9090:9090 -v <PATH_TO_prometheus.yml_FILE>:/etc/prometheus/prometheus.yml prom/prometheus --config.file=/etc/prometheus/prometheus.yml
```
#### Visualizing Spring Boot Metrics from Prometheus dashboard
That’s it! You can now navigate to http://localhost:9090 explore the Prometheus dashboard

![alt text](https://github.com/inteligeninfosys/ecollect-docs/blob/main/grafana/prometheus_home.png?raw=true)


### Setup Grafana in Docker
```
$ docker run -d --name grafana -p 3000:3000 grafana/grafana
```
#### Integrate Grafana with Prometheus metrics
You can now navigate to http://localhost:3000 and log in to Grafana with the default username admin and password admin. You can see the home page as follows

![alt text](https://github.com/inteligeninfosys/ecollect-docs/blob/main/grafana/grafana_home.png?raw=true)

#### After that click on Add Data Source and select Prometheus

![alt text](https://github.com/inteligeninfosys/ecollect-docs/blob/main/grafana/grafana_datasource.png?raw=true)

#### Add HTTP URL as you defined in prometheus.yml

![alt text](https://github.com/inteligeninfosys/ecollect-docs/blob/main/grafana/grafana_httpurl.png?raw=true)

After configuring the data source, Its time to create a dashboard to visualize Prometheus metrics. For that click on New Dashboard in home page and click on Choose Visualization. Here you can select visualization (Graph, Singlestat, Guage, Table, Text, etc.)

![alt text](https://github.com/inteligeninfosys/ecollect-docs/blob/main/grafana/grafana_visualization.png?raw=true)

