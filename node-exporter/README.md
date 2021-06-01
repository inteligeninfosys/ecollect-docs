### Install Prometheus Node Exporter on CentOS 8

Create Node Exporter System User
To run the Node Exporter safely, you need to create a user for it. Hence, run the commands below to create a non-login node_exporter user.
```
useradd -M -r -s /bin/false node_exporter
```
This will create a node_exporter user with the same group as the username.
```
id node_exporter
uid=994(node_exporter) gid=991(node_exporter) groups=991(node_exporter)
```
Download and Install Node Exporter on CentOS 8
Next, navigate to Prometheus downloads (https://prometheus.io/download#node_exporter) page and grab the latest version of Node Exporter tarball (v0.18.1 as of this writing).

```
wget https://github.com/prometheus/node_exporter/releases/download/v0.18.1/node_exporter-0.18.1.linux-amd64.tar.gz -P /tmp
```
Once the download is done, run the command below to extract it.
```
cd /tmp
tar xzf node_exporter-0.18.1.linux-amd64.tar.gz
```
To install the Node Exporter, you just have to copy the node_exporter binary from the archive folder to /usr/local/bin.
```
cp node_exporter-0.18.1.linux-amd64/node_exporter /usr/local/bin/
```
Set the user and group ownership of the node_exporter binary to node_exporter user created above.
```
chown node_exporter:node_exporter /usr/local/bin/node_exporter
```
Running Node Exporter
Create Node Exporter SystemD Service
To run the Node Exporter as a service, you need to create a Systemd service file for it.
```
vim /etc/systemd/system/node_exporter.service
[Unit]
Description=Prometheus Node Exporter
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
As stated in the Collectors section, you can configure Node Exporter to expose specific system metrics. For example, to collect CPU, Disk usage and memory statistics, you would set the ExecStart line as;
```
ExecStart=/usr/local/bin/node_exporter --collector.cpu --collector.meminfo --collector.loadavg --collector.filesystem
```
After that, reload the systemd manager configuration.
```
systemctl daemon-reload
```
Start and enable Node Exporter to run on system boot.
```
systemctl enable --now node_exporter.service
```
Check the status;
```
systemctl status node_exporter
● node_exporter.service - Prometheus Node Exporter
   Loaded: loaded (/etc/systemd/system/node_exporter.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2019-11-14 21:21:37 EAT; 1min 19s ago
 Main PID: 5130 (node_exporter)
    Tasks: 3 (limit: 5073)
   Memory: 4.4M
   CGroup: /system.slice/node_exporter.service
           └─5130 /usr/local/bin/node_exporter

```
The Node Exporter runs on TCP port 9100.
```
ss -altnp | grep 91
LISTEN   0         128                       *:9100                   *:*        users:(("node_exporter",pid=5130,fd=3))
```
Open Port 9100 on FirewallD
To allow remote connection to Node Exporter from Prometheus server only, you can use Firewalld rich rules as follows;
```
firewall-cmd --add-rich-rule 'rule family="ipv4" source address="192.168.43.250/32" port port=9100 protocol=tcp accept' --permanent
firewall-cmd --reload
```
Add Node Exporter Target to Prometheus
Once the Node Exporter is installed, you can now add the target to Prometheus server so it can be scraped.

Therefore, on Prometheus server, open the /etc/prometheus/prometheus.yml and add the node as shown below;
```
vim /etc/prometheus/prometheus.yml
...
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=` to any timeseries scraped from this config.
  - job_name: 'prometheus'

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
    - targets: ['localhost:9090']
  ## Add Node Exporter
  - job_name: 'node01'
    scrape_interval: 5s
    static_configs:
    - targets: ['192.168.43.147:9100']
```
Restart Prometheus service
```
systemctl restart prometheus
```
Ensure that you can connect to the remote Node Exporter port.
```
telnet 192.168.43.147 9100
Trying 192.168.43.147...
Connected to 192.168.43.147.
Escape character is '^]'.
```
Check Target Status
Login to Prometheus web interface and check status of the Nodes by navigating to Status > Targets.



