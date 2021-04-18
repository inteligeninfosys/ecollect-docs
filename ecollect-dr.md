# E-Collect DR Documentation
This documentation contains the steps to implement changes for DR
1. Database connection jdbc
2. Reports
3. Docker containers 
4. PM2 Services


## 1. Database connection jdbc
Servers: [xx.xx.xx.71, xx.xx.xx.72, xx.xx.xx.74]\
Change ***datasource.json*** with the DR connection details

```
$ sudo vi /app/apis/ecollect_oracle/server/datasource.json
{
  "oracle": {
    "host": "xx.xx.xx.xx",
    "port": "PORT",
    "database": "DATABASE",
    "password": "xxx",
    "name": "oracle",
    "tns": "",
    "user": "ecol",
    "connector": "oracle"
  }
}
```
Restart ***ecollect_apis*** service
```
$ pm2 restart ecollect_apis
```
Confirm service is running by checking the start logs
```
$ pm2 list
$ pm2 logs ecollect_apis
```

## 2. Reports
Server: [xx.xx.xx.74]\
Change tomcat JNDI Datasource configuration. Edit ***server.xml*** with the new database details
```
$ sudo vi /app/tomcat/conf/server.xml

<Resource
	name="jdbc/ecollect"
	auth="Container"
	type="javax.sql.DataSource"
	driverClassName="oracle.jdbc.driver.OracleDriver"
	url="jdbc:oracle:thin:@//xx.xx.xx.xx:PORT/DATABASE"
	username="xxxxxx"
	password="xxxxxx"
	/>
````

##### Restart tomcat
```
$ cd /app/tomcat/bin && sudo ./catalina.sh stop
$ cd /app/tomcat/bin && sudo ./catalina.sh start
```
##### Confirm reports are up running
From browser access http://your-server:8787/birt/frameset?__report=test.rptdesign&sample=my+parameter

![alt text](https://github.com/inteligeninfosys/ecollect-docs/blob/main/BRT_reprt_viewer_test.jpg?raw=true)

## 3. Docker containers
Servers: [xx.xx.xx.71, xx.xx.xx.72, xx.xx.xx.74]\
Change **env.list** with the DB details
```
$ sudo /app/config/env.list

DB_DATABASE=xxxx
DB_HOST=xx.xx.xx.xx
DB_PORT=xxxx
DB_PASSWORD=xx
DB_USER=xx
```
Restart the **oracledbapi** container
```
$ docker restart oracledbapi
```
Verify container running
```
$ docker ps
$ docker logs -f --tail 10 oracledbapi
```
N/B: If the container **oracledbapi** does not exist, start it by (use ```docker ps -a```)
```
$ docker run -d --name oracledbapi --env-file /app/config/env.list -p 6001:6001  migutak/oraclenode-apis-lb4:4.0.5
```
## 4. PM2 Services
Servers: [xx.xx.xx.71, xx.xx.xx.72, xx.xx.xx.74]\
The following are the list of PM2 Services
| Name  | Script Path  | Description |
| :------------ |:---------------| :-----|
| activity_file_upload      | /app/upload_coop/activity_file_upload.js | upload docs |
| adlogin      | /app/adlogin/index.js        |   adlogin login service |
| dd_demandhis_monitor | /app/cronjobs_letters/demandhis_monitor.js        |    picks dd from dd_his queue and post to /api/demandshistory |
| dd_generate | /app/cronjobs_letters/generate.js | generates dd letter details and sends to queue # check demands.log |
| dd_send_demands | /app/cronjobs_letters/send_letters.js | queries all pending dd and sends through email |
| dd_sms_monitor | /app/cronjobs_letters/sms_monitor.js | post sms messages to sms table |
| dd_demand_status | /app/cronjobs_letters/demand_status.js | updates dd status, sentby and datesent |
| demands_upload | /app/upload_coop/demands_upload.js |upload scanned d' letter
| ecollect_apis | /app/apis/ecollect_oracle | APIs /api |
| node-mail | /app/node-email/email.js | smtp email service
| upload_coop | /app/upload_coop/index.js | uploads file |
| xls_upload | /app/upload_coop/xls_uploads.js | upload xls data |
| cronjobs | /app/cronjobs/qall.js | drop tqall,tcards and creates |
| dailyletters(self-cure) | /app/cronjobs/index.js | update self cure |
| self-cure-cc | /app/cronjobs/index_cc.js | update self cure cc| 

Use ```pm2 list``` to list running services

# Start all applications
```
pm2 start ecosystem.config.js
```
# Start only the app named worker-app
```
pm2 start ecosystem.config.js --only worker-app
```
# Stop all
```
pm2 stop ecosystem.config.js
```
# Restart all
```
pm2 start   ecosystem.config.js
## Or
pm2 restart ecosystem.config.js
```
