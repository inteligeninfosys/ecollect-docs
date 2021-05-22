### How to move docker data directory to another location
1. Stop the docker daemon
```
sudo service docker stop
```
2. Add a configuration file to tell the docker daemon what is the location of the data directory
Using your preferred text editor add a file named daemon.json under the directory /etc/docker. The file should have this content:
```
{ 
   "data-root": "/path/to/your/docker" 
}
```
of course you should customize the location “/path/to/your/docker” with the path you want to use for your new docker data directory.

3. Copy the current data directory to the new one
```
sudo rsync -aP /var/lib/docker/ /path/to/your/docker
```
4. Rename the old docker directory
```
sudo mv /var/lib/docker /var/lib/docker.old
```
This is just a sanity check to see that everything is ok and docker daemon will effectively use the new location for its data.

5. Restart the docker daemon
```
sudo service docker start
```
6. Test
If everything is ok you should see no differences in using your docker containers. When you are sure that the new directory is being used correctly by docker daemon you can delete the old data directory.
```
sudo rm -rf /var/lib/docker.old
```
