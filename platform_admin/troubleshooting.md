## Backups and Restoration
 
```sh
# Backup
/opt/sas/config/Lev1/sas.servers stop 
/opt/sas/config/Lev1/sas.servers status 
 
mkdir /opt/sas/resources/backups 
cd /opt/sas/ 
sudo tar -cvpzf /opt/sas/resources/backups/after_cluster.tar.gz ./config 
 
/opt/sas/config/Lev1/sas.servers start 
/opt/sas/config/Lev1/sas.servers status 

# Restore
ls –l /opt/sas/resources.backups 
 
cd /opt/sas 
rm –rf config 
 
sudo shutdown –r now 
 
# for zipped archives
sudo tar xpzf /opt/sas/resources/backups/after_va.tar.gz 
 
OR 
 
# for tar archives
sudo tar xpf /opt/sas/resources/backups/troubleshoot.tar 
```
 

# Logs Locations

Reference Instructions.html for the logs of each server.
 
## Metadata Logs 
 
```sh
[sas@meta Logs]$ pwd 
/opt/sas/config/Lev1/SASMeta/MetadataServer/Logs 
``` 
 
## Object Spawner Logs on Compute 
 
```sh
[sas@compute Logs]$ pwd 
/opt/sas/config/Lev1/ObjectSpawner/Logs 
```
 
Object Spawner uses sastrust to do its work. 


## Metadata Cluster Verification

SMC > Plugins > Environment Manager > Metadata Manager > Right Click on Active Server > Properties > Cluster tab 


## Services NOT started by sas.servers

It's good to know how to start the following servers if something goes wrong.

- **Dataflux Data Administration Server** on Compute tier (SASApp tier)

```sh
# Config File - contains license, qkb path
/opt/sas/SASHome/DataFluxDataManagementServer/2.7/dmserver/etc/app.cfg

# Server Control
/opt/sas/SASHome/DataFluxDataManagementServer/2.7/dmserver/bin/dmsadmin status
```

- Deployment Tester
- Visual Process Orchestration Server

These are started automatically after a successful config pass, but are NOT automatically restarted.

- **Deployment Agent (on each machine)**
- **Environment Manger Agent (on each machine)**


## Issue with Environment URL

This can affect being able to launch programs such as Enterprise Miner.

Each machine has a file in `SASHome` called `sassw.config` which stores a few values including:

```sh
SASENVIRONMENTSURL=http://webapp.demo.sas.com:7980/sas/sas-environment.xml
```

Fix each machine which has the invalid URL.


## Wrong License on VA

License files are located in `/SASdepot/SAS9.4M4/sid_files/<LICENSE>.txt`.

Apply a new sid file and ignore what's alraedy there:

```sh
/opt/sas/SASHome/SASDeploymentManager/9.4/sasdm.sh -nosidvalidate &
```

Without `-nosidvalidate` SDM would complain that you're adding a site number which doesn't exist in the current deployment.

## Environment Agents

### Wrong Web Server on agent.properties file

Within `agent.properties` the wrong value for Enviroment Manager (camIP) could be used.

```sh
# File: /opt/sas/config/Lev1/Web/SASEnvironmentManager/agent-5.8.0-EE/conf/agent.properties
# Fix value
agent.setup.camIP=webapp.demo.sas.com

# Delete ./data directory if it exists
/opt/sas/config/Lev1/Web/SASEnvironmentManager/agent-5.8.0-EE/data

# Then restart agent
./hq-agent.sh restart
```

### Wrong SAS Home Location

Run `setup.exe` with the `-changesashome` option.

```
setup.exe –record –deploy –responsefile -changesashome D:\SAS\resources\client_install.txt
```


## Platform Suite Removal

The issue is if you put the wrong host in the LSF_MASTER_LIST parameter in the `install.config` file. Need to use **compute hostname** and NOT the metadata server.

```sh
# stop LSF-related processes
ps -ef | grep lsf

# remove these directories
/opt/sas/thirdparty/share
/opt/sas/thirdparty/pm
/opt/sas/resources/pm_install
```

