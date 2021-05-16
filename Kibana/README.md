
### Elasticsearch and Kibana with TLS

Generate self-signed certs
```
$ openssl req -newkey rsa:2048 -nodes -keyout domain.key -x509 -days 3065 -out domain.crt \
-subj "/C=KE/ST=Nairobi/L=Nairobi/O=inteligen/CN=52.117.54.216.nip.io"
```
Start elasticsearch container in dev/testing mode
```
mkdir esdatadir
chmod g+rwx esdatadir
chgrp 1000 esdatadir

$ docker network create elastic
$ cat env.list 
node.name=es01
cluster.name=es-docker-cluster
#bootstrap.memory_lock=true
discovery.seed_hosts=es01
discovery.seed_providers=
cluster.initial_master_nodes=es01
"ES_JAVA_OPTS=-Xms512m -Xmx512m"
"discovery.type=single-node"

$ docker pull docker.elastic.co/elasticsearch/elasticsearch:7.12.1

$ docker run -it -d --name es01 --net elastic -p 9200:9200 -p 9300:9300 \
--env-file=env.list -e "discovery.type=single-node" \
-v /root/esdatadir:/usr/share/elasticsearch/data \
docker.elastic.co/elasticsearch/elasticsearch:7.12.1
```
Start Kibana and connect it to your Elasticsearch container
```
$ docker pull docker.elastic.co/kibana/kibana:7.12.1

$ docker run -it -d --name kib01 --net elastic -p 5601:5601 \
-v ./kibana.yml:/usr/share/kibana/config/kibana.yml \
-v ./domain.crt:/etc/kibana/config/certs/domain.crt \
-v ./domain.key:/etc/kibana/config/certs/domain.key  \
docker.elastic.co/kibana/kibana:7.12.1
```
To access Kibana, go to https://52.117.54.216.nip.io:5601

To stop your containers, run:
```
$ docker stop es01
$ docker stop kib01
```
To remove the containers and their network, run:
```
$ docker network rm elastic
$ docker rm es01-test
$ docker rm kib01-test
```
