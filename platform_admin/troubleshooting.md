# Backups and Restoration
 
```
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
 
```
[sas@meta Logs]$ pwd 
/opt/sas/config/Lev1/SASMeta/MetadataServer/Logs 
``` 
 
## Object Spawner Logs on Compute 
 
```
[sas@compute Logs]$ pwd 
/opt/sas/config/Lev1/ObjectSpawner/Logs 
```
 
Object Spawner uses sastrust to do its work. 


## Metadata Cluster Verification

SMC > Plugins > Environment Manager > Metadata Manager > Right Click on Active Server > Properties > Cluster tab 


# Services NOT started by sas.servers

It's good to know how to start the following servers if something goes wrong.

- **Dataflux Data Administration Server** on Compute tier (SASApp tier)
- Deployment Tester
- Visual Process Orchestration Server

These are started automatically after a successful config pass, but are NOT automatically restarted.

- **Deployment Agent (on each machine)**
- **Environment Manger Agent (on each machine)**
