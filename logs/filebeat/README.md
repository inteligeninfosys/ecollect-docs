Obtaining Filebeat for Docker
```
docker pull docker.elastic.co/beats/filebeat:7.13.0
```
Running Filebeat with the setup command will create the index pattern and load visualizations
```
docker run \
docker.elastic.co/beats/filebeat:7.13.0 \
setup -E setup.kibana.host=kibana:5601 \
-E output.elasticsearch.hosts=["elasticsearch:9200"]
```
Download this example configuration file as a starting point:
```
curl -L -O https://raw.githubusercontent.com/elastic/beats/7.13/deploy/docker/filebeat.docker.yml
```
configure Filebeat on Docker is to provide filebeat.docker.yml
```
docker run -d \
  --name=filebeat \
  --user=root \
  --volume="$(pwd)/filebeat.docker.yml:/usr/share/filebeat/filebeat.yml:ro" \
  --volume="/var/lib/docker/containers:/var/lib/docker/containers:ro" \
  --volume="/var/run/docker.sock:/var/run/docker.sock:ro" \
  docker.elastic.co/beats/filebeat:7.13.0 filebeat -e -strict.perms=false \
  -E output.elasticsearch.hosts=["elasticsearch:9200"] 
 ```
 Add labels to your application Docker containers, and they will be picked up by the
 ```
 docker run \
  --label co.elastic.logs/module=apache2 \
  --label co.elastic.logs/fileset.stdout=access \
  --label co.elastic.logs/fileset.stderr=error \
  --label co.elastic.metrics/module=apache \
  --label co.elastic.metrics/metricsets=status \
  --label co.elastic.metrics/hosts='${data.host}:${data.port}' \
  --detach=true \
  --name my-apache-app \
  -p 8080:80 \
  httpd:2.4
 ```
