
specify the docker metrics-address
```
$ /etc/docker/daemon.json
{
  "metrics-addr" : "0.0.0.0:9323",
  "experimental" : true
}

$ docker run -it -d --name grafana -p 3000:3000 grafana/grafana

$ docker run -it -d --name prometheus -v /root/prometheus.yml:/etc/prometheus/prometheus.yml -p 9090:9090 prom/prometheus

$ docker run -it --rm --name monitoring_cadvisor \
-v /:/rootfs:ro -v /var/run:/var/run:rw -v /sys:/sys:ro -v /var/lib/docker/:/var/lib/docker:ro \
-p 8084:8080 google/cadvisor:latest 

$ docker run -d -p 9100:9100 \
  -v "/:/host:ro,rslave" quay.io/prometheus/node-exporter:latest
```

