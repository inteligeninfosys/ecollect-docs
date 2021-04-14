# E-Collect DR Documentation
This documentation contains the steps to implement changes for DR
1. Database connection jdbc
2. Reports
3. Docker containers 


## 1. Database connection jdbc
Servers: [xx.xx.xx.71, xx.xx.xx.72, xx.xx.xx.74]\
Change ***datasource.json*** with the DR connection details

```
$ sudo vi /app/apis/ecollect_apis/server/datasource.json
{
  "oracle": {
    "host": "xx.xx.xx.xx",
    "port": "1521",
    "database": "ECOLLECT",
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
$ sudo vi /app/tomcat/server/server.xml

<Resource name="jdbc/ecollect" 
	auth="Container" 
	type="javax.sql.DataSource"
        maxTotal="100" 
        maxIdle="30" 
        maxWaitMillis="10000"
        username="ecol" 
        password="xxx" 
	driverClassName="oracle.jdbc.OracleDriver"
        url="jdbc:mysql://xxxxx:3306/ECOLLECT"/>
````

##### Restart tomcat
```
$ sudo ./app/tomcat/bin/catalina.sh stop
$ sudo ./app/tomcat/bin/catalina.sh start
```
## 3. Docker containers
Servers: [xx.xx.xx.71, xx.xx.xx.72, xx.xx.xx.74]\
Change environment variables for docker containers. Stop the running container and start a new one with the DR connection details.

##### Stop & start DR container
```
$ docker stop oracledbapi
$ docker start oracledbapibcs
```
N/B: The following cmd are used to create the two containers. You **Do not** need to run these if the containers (oracledbapi, oracledbapibcs) already exists. Use ``` $ docker ps -a ```
```
$ docker run -d --name oracledbapi -e DB_DATABASE='ECOLLECT' -e DB_HOST='xx.xx.xx.xx' -e DB_PORT=1500 -e DB_PASSWORD='xxx' -e DB_USER='xxx' -p 6001:6001 migutak/oraclenode-apis-lb4:4.0.5

$ docker run -d --name oracledbapibcs -e DB_DATABASE='ECOLLECT' -e DB_HOST='xx.xx.xx.xx' -e DB_PORT=1500 -e DB_PASSWORD='xxx' -e DB_USER='xxx' -p 6001:6001 migutak/oraclenode-apis-lb4:4.0.5
```
Verify container running
```
$ docker ps
$ docker logs oracledbapi
or
$ docker logs oracledbapibcs
```
