# E-Collect Application deployment
## Prerequisites
- nginx.conf 
- ecollect.crt
- ecollect.key

For self signed certs, generate by
```
$ openssl req -newkey rsa:2048 -nodes -keyout ecollect.key -x509 -days 3065 -out ecollect.crt -subj "/C=US/ST=Nairobi/L=Nairobi/O=Inteligen/ CN=52.117.54.216.nip.io"
```
## 1. New Deployment
Build ecollect. From the project root folder run
```
$ ng build --configuration=do|uat|prod
```
Copy the generate 'ecollectv5' folder into the server /app directory
```
$ scp -r ecollect root@157.245.253.243:/app/
```
Copy Server cert and key into the /app/certs directory
```
$ scp ecollect.crt root@xx.xx.xx.216:/app/certs/
$ scp ecollect.key root@xx.xx.xx.216:/app/certs/
```
Copy nginx.conf into the /app/configs directory
```
$ scp nginx.conf root@xx.xx.xx:/app/configs/
```
ssh into the server
```
$ ssh root@157.245.253.243
```
Start E-Collect
```
$ docker-compose restart ecollectv5;

***ignore***
$ docker run -d --name ecollect -p 443:443 \
-v /app/configs/nginx.conf:/etc/nginx/nginx.conf \
-v /app/certs/ecollect.key:/etc/nginx/ecollect.key \
-v /app/certs/ecollect.crt:/etc/nginx/ecollect.crt \
-v /app/ecollect:/usr/share/nginx/html \
-e TZ=Africa/Nairobi \
--log-driver json-file --log-opt max-size=1k --log-opt max-file=30 \
migutak/nginx:1.0
***ignore***
```
Confirm running
```
$ docker ps | grep ecollectv5
```
Navigate to https://157.245.253.243:4430

## 2. Update E-Collect
Build ecollect. From the project root folder run
```
$ ng build --configuration=do|uat|prod
```
Copy the generate 'ecollectv5' folder into the server /app directory
```
$ scp -r ecollectv5 root@157.245.253.243:/app/
```
ssh into the server
```
$ ssh root@157.245.253.243
```
Restart E-Collect
```
$ docker-compose restart ecollectv5
```
Confirm running
```
$ docker ps | grep ecollectv5
```
Navigate to https://157.245.253.243:4430 \
![alt text](https://github.com/inteligeninfosys/ecollect-docs/blob/main/ecollectapp.JPG?raw=true)
