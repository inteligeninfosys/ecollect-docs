

E-Collect app
```
docker run -it -d --name ecollect --restart always \
-v /app/certs/2021/ecollectweb.crt:/etc/nginx/ecollectweb.co-opbank.co.ke.crt \
-v /app/certs/2021/ecollectweb.key:/etc/nginx/ecollectweb.co-opbank.co.ke.key \
-v /app/configs/nginx.conf:/etc/nginx/nginx.conf \
-v /app/ecollect:/usr/share/nginx/html \
-v /app/nginx_logs:/var/log/nginx \
--log-driver json-file --log-opt max-size=10m --log-opt max-file=30 \
-p 80:80 -p 443:443 \
-e TZ=Africa/Nairobi nginx:coop
```
ecollect-apis
```
$ docker run -d --name ecollect_apis -p 8000:8000  
-e DB_DATABASE=xxxx
-e DB_HOST=xx.xx.xx.xx
-e DB_PORT=xxxx
-e DB_PASSWORD=xx
-e DB_USER=xx
-e TZ=Africa/Nairobi \
--log-driver json-file --log-opt max-size=1k --log-opt max-file=30 \
migutak/ecollect_apis:4.1
```
oraclenodeapis
```
$ docker run -d --name oracledbapi -p 6001:6001  
-e DB_DATABASE=xxxx
-e DB_HOST=xx.xx.xx.xx
-e DB_PORT=xxxx
-e DB_PASSWORD=xx
-e DB_USER=xx
-e TZ=Africa/Nairobi \
--log-driver json-file --log-opt max-size=1k --log-opt max-file=30 \
migutak/oraclenode-apis-lb4:4.0.5
```
docx
```
docker run -it -d --name docx -p 8004:8004 \
-e FILEPATH=/app/nfsmount/demandletters/ \
-e SENDEMAILURL=http://172.16.204.72:8005/ipfcancellation/email \
-e TZ=Africa/Nairobi \
--log-driver json-file --log-opt max-size=1k --log-opt max-file=30 \
migutak/docxletters:4.1
```
node-email
```
docker run -it -d --name node-email -p 8005:8005 \
-e FILEPATH=/app/nfsmount/demandletters/ \
-e IMAGEPATH=/home/ecollectadmin/docxletters/routes/ \,
-e SMTPSERVER=office365.officer \,
-e SMTPPORT=587 \,
-e USER=xxxx@co-opbank.co.ke \,
-e pass='xxx' \
-e TZ=Africa/Nairobi \
--log-driver json-file --log-opt max-size=1k --log-opt max-file=30 \
migutak/node-email:4.1
```
