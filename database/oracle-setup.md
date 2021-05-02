## Oracle 18.4 XE on a Docker Container!
### Step 1
- Download Oracle’s image builder from https://github.com/oracle/docker-images/tree/master/OracleDatabase/SingleInstance
- Extract it a directory of your liking
- This same GitHub directory also includes detailed instruction
Please note: the image builder supports the following Oracle versions: 11.2.0.2 12.1.0.2 12.2.0.1 18.3.0 18.4.0 19.3.0
### Step 2
```
[oracle@localhost dockerfiles]$ ./buildContainerImage.sh -h

Usage: buildContainerImage.sh -v [version] -t [image_name:tag] [-e | -s | -x] [-i] [-o] [container build option]
Builds a container image for Oracle Database.

Parameters:
   -v: version to build
       Choose one of: 11.2.0.2  12.1.0.2  12.2.0.1  18.3.0  18.4.0  19.3.0  
   -t: image_name:tag for the generated docker image
   -e: creates image based on 'Enterprise Edition'
   -s: creates image based on 'Standard Edition 2'
   -x: creates image based on 'Express Edition'
   -i: ignores the MD5 checksums
   -o: passes on container build option

* select one edition only: -e, -s, or -x

LICENSE UPL 1.0

Copyright (c) 2014,2021 Oracle and/or its affiliates.
```
Build a Docker image by using the image builder:
```
./buildDockerImage.sh -v 18.4.0 -x

 Oracle Database container image for 'xe' version 18.4.0 is ready to be extended: 
    --> oracle/database:18.4.0-xe
  Build completed in 15075 seconds.
```
### Step 3
Running the Docker Image:
```
docker run --name <container name> \
-p <host port>:1521 -p <host port>:5500 \
-e ORACLE_PWD=<your database passwords> \
-e ORACLE_CHARACTERSET=<your character set> \
-v [<host mount point>:]/opt/oracle/oradata \
oracle/database:18.4.0-xe

Parameters:
   --name:        The name of the container (default: auto generated)
   -p:            The port mapping of the host port to the container port.
                  Two ports are exposed: 1521 (Oracle Listener), 5500 (EM Express)
   -e ORACLE_PWD: The Oracle Database SYS, SYSTEM and PDB_ADMIN password (default: auto generated)
   -e ORACLE_CHARACTERSET:
                  The character set to use when creating the database (default: AL32UTF8)
   -v /opt/oracle/oradata
                  The data volume to use for the database.
                  Has to be writable by the Unix "oracle" (uid: 54321) user inside the container!
                  If omitted the database will not be persisted over container recreation.
   -v /opt/oracle/scripts/startup | /docker-entrypoint-initdb.d/startup
                  Optional: A volume with custom scripts to be run after database startup.
                  For further details see the "Running scripts after setup and on startup" section below.
   -v /opt/oracle/scripts/setup | /docker-entrypoint-initdb.d/setup
                  Optional: A volume with custom scripts to be run after database setup.
                  For further details see the "Running scripts after setup and on startup" section below.
  ```
Sample command to start a container would be
  ```
$ docker run -it -d --name myxedb --restart always --cpuset-cpus="0-7" -p 51521:1521 -p 55500:5500 -v /opt/oracle/oradata:/opt/oracle/oradata -e ORACLE_PWD=mysecurepassword -e ORACLE_CHARACTERSET=AL32UTF8 oracle/database:18.4.0-xe
 
ORACLE_CHARACTERSET=AL32UTF8 oracle/database:18.4.0-xe
ORACLE PASSWORD FOR SYS AND SYSTEM: mysecurepassword
Specify a password to be used for database accounts. Oracle recommends that the password entered should be at least 8 characters in length, contain at least 1 uppercase character, 1 lower case character and 1 digit [0-9]. Note that the same password will be used for SYS, SYSTEM and PDBADMIN accounts:
Confirm the password:
Configuring Oracle Listener.
Listener configuration succeeded.
Configuring Oracle Database XE.
...
Running Custom Scripts
100% complete
Database creation complete. For details check the logfiles at:
 /opt/oracle/cfgtoollogs/dbca/XE.
Database Information:
Global Database Name:XE
System Identifier(SID):XE
Look at the log file "/opt/oracle/cfgtoollogs/dbca/XE/XE.log" for further details.

Connect to Oracle Database using one of the connect strings:
     Pluggable database: 1104d5676df7/XEPDB1
     Multitenant container database: 1104d5676df7
Use https://localhost:5500/em to access Oracle Enterprise Manager for Oracle Database XE
The Oracle base remains unchanged with value /opt/oracle
#########################
DATABASE IS READY TO USE!
#########################
  ```
Once the container has been started and the database created you can connect to it just like to any other database:
  ```
  $ sqlplus sys/<your password>@//localhost:1521/XE as sysdba
  $ sqlplus system/<your password>@//localhost:1521/XE
  $ sqlplus pdbadmin/<your password>@//localhost:1521/XEPDB1
  
  [root@ecollect ~]# docker exec -it mydb /bin/bash
   bash-4.2# ps -ef | grep pmon
   oracle      2310       1  0 12:20 ?        00:00:00 xe_pmon_XE
   root        2881    2874  0 12:24 pts/0    00:00:00 grep pmon
   
   create user ecol identified by ecol;
   quota unlimited on users;
   grant connect, resource, dba to ecol;
  ```
The Oracle Database inside the container also has Oracle Enterprise Manager Express configured. To access OEM Express, start your browser and follow the URL:
  ```
  https://localhost:5500/em/
  ```
On the first startup of the container a random password will be generated for the database if not provided. You can find this password in the output line:
```
ORACLE PASSWORD FOR SYS AND SYSTEM:
```
The password for those accounts can be changed via the docker exec command. Note, the container has to be running:
```
$ docker exec <container name> /opt/oracle/setPassword.sh <your password>
```

### Step 5
The following enables connecting to EM Express from any host. Just connect to the database and run:
```
$ exec dbms_xdb.setlistenerlocalaccess(false);
PL/SQL procedure successfully completed.
```
### Running SQL*Plus in a container
You may use the same container image you used to start the database, to run sqlplus to connect to it, for example:
```
docker run --rm -ti oracle/database:19.3.0-ee sqlplus pdbadmin/<yourpassword>@//<db-container-ip>:1521/ORCLPDB1
```
Another option is to use docker exec and run sqlplus from within the same container already running the database:
```
docker exec -ti <container name> sqlplus pdbadmin@ORCLPDB1
```
Ref: https://github.com/oracle/docker-images/issues/1522 [Specified value of sga_target 1536M is too small, needs to be at least 1968M]
