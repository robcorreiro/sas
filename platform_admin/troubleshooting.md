# Backups 
 
```
/opt/sas/config/Lev1/sas.servers stop 
/opt/sas/config/Lev1/sas.servers status 
 
mkdir /opt/sas/resources/backups 
cd /opt/sas/ 
sudo tar -cvpzf /opt/sas/resources/backups/after_cluster.tar.gz ./config 
 
/opt/sas/config/Lev1/sas.servers start 
/opt/sas/config/Lev1/sas.servers status 
 ```
 
## Restore 

```
ls –l /opt/sas/resources.backups 
 
cd /opt/sas 
rm –rf config 
 
sudo shutdown –r now 
 
# for zipped 
sudo tar xpzf /opt/sas/resources/backups/after_va.tar.gz 
 
 
OR 
 
# for tar 
sudo tar xpf /opt/sas/resources/backups/troubleshoot.tar 
```
 

# Logs Locations

Note: Instructions.html contains the locations for each server's logs. 
 
Metadata Logs 
 
[sas@meta Logs]$ pwd 
/opt/sas/config/Lev1/SASMeta/MetadataServer/Logs 
 
 
Object Spawner Logs on Compute 
 
[sas@compute Logs]$ pwd 
/opt/sas/config/Lev1/ObjectSpawner/Logs 
 
 
Object Spawner uses sastrust to do its work. 


# Metadata Cluster Verification

SMC > Plugins > Environment Manager > Metadata Manager > Right Click on Active Server > Properties > Cluster tab 
