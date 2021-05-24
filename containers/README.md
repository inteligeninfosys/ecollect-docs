
1. E-Collect app
```
docker run -it -d --name ecollect --restart always \
-v /app/certs/2021/ecollectweb.crt:/etc/nginx/ecollectweb.co-opbank.co.ke.crt \
-v /app/certs/2021/ecollectweb.key:/etc/nginx/ecollectweb.co-opbank.co.ke.key \
-v /app/configs/nginx.conf:/etc/nginx/nginx.conf \
-v /app/ecollect:/usr/share/nginx/html \
-v /app/nginx_logs:/var/log/nginx \
--log-driver json-file --log-opt max-size=10m --log-opt max-file=30 \
-p 80:80 -p 443:443 \
-e TZ=Africa/Nairobi 172.16.204.72:5100/nginx:coop
```
2. ecollect-apis
```
$ docker run -d --name ecollect_apis -p 8000:8000  --restart always \
-e DB_DATABASE=ECOLLECT \
-e REDIS_HOST=ecollectweb.co-opbank.co.ke \
-e DB_HOST=xx.xx.xx.xx \
-e DB_PORT=xxxx \
-e DB_PASSWORD=xx \
-e DB_USER=xx \
-e TZ=Africa/Nairobi \
--log-driver json-file --log-opt max-size=10m --log-opt max-file=30 \
172.16.204.72:5100/ecollect_apis:4.1
```
3. oraclenodeapis
```
$ docker run -d --name oracledbapi -p 6001:6001 --restart always \
-e DB_DATABASE=xxxx \
-e DB_HOST=xx.xx.xx.xx \
-e DB_PORT=xxxx \
-e DB_PASSWORD=xx \
-e DB_USER=xx \
-e TZ=Africa/Nairobi \
--log-driver json-file --log-opt max-size=10m --log-opt max-file=30 \
172.16.204.72:5100/oraclenode-apis-lb4:4.0.5
```
4. docx
```
docker run -it -d --name docx -p 8004:8004 --restart always \
-e FILEPATH=/app/nfs/demandletters/ \
-e SENDEMAILURL=http://172.16.204.72:8005/ipfcancellation/email \
-e TZ=Africa/Nairobi \
--log-driver json-file --log-opt max-size=10m --log-opt max-file=30 \
172.16.204.72:5100/docxletters:4.1
```
5. node-email
```
docker run -it -d --name node-email -p 8005:8005 --restart always \
-e FILEPATH=/app/nfs/demandletters/ \
-e IMAGEPATH=/home/ecollectadmin/docxletters/routes/ \
-e SMTPSERVER=office365.officer \
-e SMTPPORT=587 \
-e USER=xxxx@co-opbank.co.ke \
-e pass='xxx' \
-e TZ=Africa/Nairobi \
--log-driver json-file --log-opt max-size=10m --log-opt max-file=30 \
172.16.204.72:5100/node-email:4.1
```
6. adlogin
```
$ docker run -it -d --name adlogin -p 6650:6650 --restart always \
-e TZ=Africa/Nairobi \
--log-driver json-file --log-opt max-size=10m --log-opt max-file=30 \
172.16.204.72:5100/adlogin:4.1
```
7. rabbitmq
```
$ docker run -it -d --name rabbitmq -p 5672:5672 -p 15672:15672 --restart always \
-e TZ=Africa/Nairobi \
--log-driver json-file --log-opt max-size=10m --log-opt max-file=30 \
rabbitmq:3-management
```
8. crondailyletters\
update demandsdue set status = 'self cure' where status = 'pending' and accnumber not in (select accnumber from loans_stage)\
insert into demandsdue active letters from tqall\
create table tqall as select * from qall \
create table tcards as select * from qcards where primary = 'P'
```
$ mkdir /app/cronlogs

$ docker run -it -d --name crondailyletters --restart always \
-e DB_USER=ecol -e DB_PASSWORD=ecol \
-e DB_CONNECTIONSTRING=xx.xx.54.217:1521/ORCLCDB.localdomain  \
-v /app/cronlogs:/tmp \
--label ofelia.enabled=true \
--label ofelia.job-exec.crondailyletters-job.schedule="0 0 4 * * *" \
--label ofelia.job-exec.crondailyletters-job.command="node index_manual.js >> /tmp/crondailyletters.log 2>&1" \
-e TZ=Africa/Nairobi \
172.16.204.72:5100/crondailyletters:5.0

$ docker run -it -d --name crondailyletters-cc --restart always \
-e DB_USER=ecol -e DB_PASSWORD=ecol \
-e DB_CONNECTIONSTRING=xx.xx.54.217:1521/ORCLCDB.localdomain  \
-e SERVICENAME=Letters-cc \
-v /app/cronlogs:/tmp \
--label ofelia.enabled=true \
--label ofelia.job-exec.crondailyletters-job.schedule="0 0 6 * * *" \
--label ofelia.job-exec.crondailyletters-job.command="node index_cc.js >> /tmp/crondailyletters-cc.log 2>&1" \
-e TZ=Africa/Nairobi \
172.16.204.72:5100/crondailyletters:5.0

$ docker run -it -d --name cronqall --restart always \
-e DB_USER=ecol -e DB_PASSWORD=ecol \
-e DB_CONNECTIONSTRING=xx.xx.54.217:1521/ORCLCDB.localdomain  \
-e SERVICENAME=qall \
-v /app/cronlogs:/tmp \
--label ofelia.enabled=true \
--label ofelia.job-exec.crondailyletters-job.schedule="0 0 5 * * *" \
--label ofelia.job-exec.crondailyletters-job.command="node index_cc.js >> /tmp/qall.log 2>&1" \
-e TZ=Africa/Nairobi \
172.16.204.72:5100/crondailyletters:5.0

$ docker run -it -d --name crondailyletters-cron --restart always \
    -v /var/run/docker.sock:/var/run/docker.sock:ro \
        172.16.204.72:5100/ofelia:latest daemon --docker
```
9. uploads_coop

```
$ docker run -it -d --name uploads_coop -p 4000:4000 -p 3000:3000 --restart always \
-e FILEPATH=/app/nfs/uploads/ \
-e TZ=Africa/Nairobi \
--log-driver json-file --log-opt max-size=10m --log-opt max-file=30 \
172.16.204.72:5100/uploads:5.0
```
10. Demand letter delivery

```
$ docker run -it -d --name lettersdelivery --restart always \
-e DB_USER=ecol -e DB_PASSWORD=ecol -e NODE_ORACLEDB_CONNECTIONSTRING=xx.xx.54.217:1521/ORCLCDB.localdomain \
-e RABBITMQ=amqp://guest:guest@ecollectweb.co-opbank.co.ke \
-e LETTERGENERATEURL=http://172.16.204.71:8004/docx/ \
-e SENDEMAILURL=http://172.16.204.71:8005/demandemail/email \
-e API=http://172.16.204.71:8000 \
-e NODEAPI=http://172.16.204.71:6001/nodeapi \
-e TZ=Africa/Nairobi \
--log-driver json-file --log-opt max-size=10m --log-opt max-file=30 \
172.16.204.72:5100/letterdelivery:5.0
```


