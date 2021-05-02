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
  docker run --name oracle -p 1521:1521 -p 5500:5500 -e ORACLE_PWD=root -v /opt/oracle/oradata:/opt/oracle/oradata oracle/database:18.4.0-xe
  ```
Once the container has been started and the database created you can connect to it just like to any other database:
  ```
  $ sqlplus sys/<your password>@//localhost:1521/XE as sysdba
  $ sqlplus system/<your password>@//localhost:1521/XE
  $ sqlplus pdbadmin/<your password>@//localhost:1521/XEPDB1
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
