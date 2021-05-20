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
$ ng build --prod
```
Copy the generate 'ecollect' folder into the server /app directory
```
$ scp -r ecollect root@52.117.54.216:/app/
```
Copy Server cert and key into the /app/certs directory
```
$ scp ecollect.crt root@52.117.54.216:/app/certs/
$ scp ecollect.key root@52.117.54.216:/app/certs/
```
Copy nginx.conf into the /app/configs directory
```
$ scp nginx.conf root@52.117.54.216:/app/configs/
```
ssh into the server
```
$ ssh root@52.117.54.216
```
Start E-Collect
```
$ docker run -d --name ecollect -p 443:443 \
-v /app/configs/nginx.conf:/etc/nginx/nginx.conf \
-v /app/certs/ecollect.key:/etc/nginx/ecollect.key \
-v /app/certs/ecollect.crt:/etc/nginx/ecollect.crt \
-v /app/ecollect:/usr/share/nginx/html \
-e TZ=Africa/Nairobi \
--log-driver json-file --log-opt max-size=1k --log-opt max-file=30 \
nginx
```
Confirm running
```
$ docker ps | grep ecollect
```
Navigate to https://52.117.54.216.nip.io

## 2. Update E-Collect
Build ecollect. From the project root folder run
```
$ ng build --prod
```
Copy the generate 'ecollect' folder into the server /app directory
```
$ scp -r ecollect root@52.117.54.216:/app/
```
ssh into the server
```
$ ssh root@52.117.54.216
```
Restart E-Collect
```
$ docker restart ecollect
```
Confirm running
```
$ docker ps | grep ecollect
```
Navigate to https://52.117.54.216.nip.io \
![alt text](https://github.com/inteligeninfosys/ecollect-docs/blob/main/ecollectapp.JPG?raw=true)
