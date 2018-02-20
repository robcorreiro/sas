# Preinstall Reference File

- SAS Installer USER/Pass, verify sudo access
- Root Pass
- Server Names
- License Files location
- /SASBackups showing your own data
- Depot location
- Plan file location
- Moba Sessions with correct machine names

- Credentials
    - sasadm@saspw
    - sastrust@saspw
    - sasdemo
    - sassrv
    - lsfadmin

# Plan

Build Reference file.

```
INSTALL

SASHome

<SAS_HOME DIR>


ENVIRONMENTS URL

http://MIDTIER_URL:7980/sas/sas-environment.xml

================================================================

CONFIG

<CONFIG DIR>

META 1

<SERVER>


ENV MANAGER (MID TIER) HOST NAME

<HOSTNAME>


BACKUP LOC

<BACKUP LOC>


sudo tar czpf /opt/sas/resources/backups/after_X.tar.gz ./config
```

Setup MobaXTerm with sessions, verify login, sudo access, write permissions to /SASBackups/Metadata

```sh
# Run on each Meta node prior to install
sudo whoami
netstat -a | grep 8561
ls -ld /SASBackups/Metadata

# Only on Meta
touch /SASBackups/Metadata/meta.confirm.rc
```

**Meta Install/Config + Meta2 Install**

```sh
<depot location>/setup.sh -record -deploy -responsefile /opt/sas/resources/meta_install_config.txt &
<depot location>/setup.sh -record -deploy -responsefile /opt/sas/resources/meta2_install.txt &
```

**Meta3 Install**

```
<depot location>/setup.sh -record -deploy -responsefile /opt/sas/resources/meta3_install.txt &
```

**Compute Install**

```sh
mkdir -p /opt/sas/thirdparty/JUnit
cp /SASdepot/ThirdParty/JUnit/junit-4.8.1.jar /opt/sas/thirdparty/JUnit/

<depot location>/setup.sh -record -deploy -responsefile /opt/sas/resources/compute_install.txt &
```

**VA Install**

```sh
mkdir -p /opt/sas/thirdparty/JUnit
cp /SASdepot/ThirdParty/JUnit/junit-4.8.1.jar /opt/sas/thirdparty/JUnit/

<depot location>/setup.sh -record -deploy -responsefile /opt/sas/resources/va_install.txt &
```

**Midtier Install**

```sh
<depot location>/setup.sh -record -deploy -responsefile /opt/sas/resources/midtier_install.txt &
```

---

**Meta2 Config**

```sh
<depot location>/setup.sh -record -deploy -responsefile /opt/sas/resources/meta2_config.txt &
```


**Meta3 Config**

```
<depot location>/setup.sh -record -deploy -responsefile /opt/sas/resources/meta3_config.txt &
```

**Compute Config**

```sh
<depot location>/setup.sh -record -deploy -responsefile /opt/sas/resources/compute_config.txt &
```

**Potential 2nd Config Pass on Compute**

```sh
<depot location>/setup.sh -record -deploy -responsefile /opt/sas/resources/compute_fsc_config.txt &
```

**VA Config**

```sh
<depot location>/setup.sh -record -deploy -responsefile /opt/sas/resources/va_config.txt &
```

**Midtier config**

```sh
<depot location>/setup.sh -record -deploy -responsefile /opt/sas/resources/midtier_config.txt &
```

**Platform Suite on Compute (any time)**

**Client Install/Config (any time)**

```
# Might not be needed
mkdir \SAS
mkdir \SAS\resources


cd \SAS9.4M4
setup.exe –record –deploy –responsefile D:\SAS\resources\client_install.txt
setup.exe –record –deploy –responsefile D:\SAS\resources\client_config.txt
```
    
---

# Detailed Validations

## After Meta

Copy Instructions.html from `/opt/sas/config/Lev1/Documents` and perform validation.

```sh
# On Meta
alias ss=/opt/sas/config/Lev1/sas.servers
ss status

netstat -a | grep 8561

# MetaData Backup in SMC
/opt/sas/SASHome/SASManagementConsole/9.4/sasmc &
```

## After Meta2

Copy Instructions.html from `/opt/sas/config/Lev1/Documents` and perform validation.

```sh
# On Meta2
alias ss=/opt/sas/config/Lev1/sas.servers
ss status

netstat -a | grep 8561
```

## After Meta3 (Metadata Cluster)


Copy Instructions.html from `/opt/sas/config/Lev1/Documents` and perform validation.

```sh
# On Meta3
alias ss=/opt/sas/config/Lev1/sas.servers
ss status

netstat -a | grep 8561

# if it doesn't exist
mkdir /opt/sas/resources/backups

# Stop sas.servers on: Meta3, Meta2, Meta1
ss stop

# MULTI-EXEC
cd /opt/sas
sudo tar czpf /opt/sas/resources/backups/after_cluster.tar.gz ./config

# Start sas.servers on: Meta1, Meta2, Meta3
ss start
```

## After Compute

Copy Instructions.html from `/opt/sas/config/Lev1/Documents` and perform validation.

```sh
# On Compute
alias ss=/opt/sas/config/Lev1/sas.servers
ss status

# MetaData Backup in SMC

# Server Validations referencing Instructions.html

# Stop sas.servers on: Compute, Meta3, Meta2, Meta1
ss stop
 
# USE MULTI-EXEC
cd /opt/sas
sudo tar czpf /opt/sas/resources/backups/after_compute.tar.gz ./config

# Start sas.servers on: Meta1, Meta2, Meta3, Compute
ss start
```

## Potential 2nd Config on Compute

Copy new Instructions.html from `/opt/sas/config/Lev1/Documents` and perform validation.

## After VA

Copy Instructions.html from `/opt/sas/config/Lev1/Documents` and perform validation.

```sh
# On VA
alias ss=/opt/sas/config/Lev1/sas.servers
ss status

# MetaData Backup in SMC

# Server Validations referencing Instructions.html

# SASAppVA - SAS DATA Step Batch Server
/opt/sas/config/Lev1/SASAppVA/BatchServer/sasbatch.sh

# Stop sas.servers on: VA, Compute, Meta3, Meta2, Meta1
ss stop
 
# USE MULTI-EXEC
cd /opt/sas
sudo tar czpf /opt/sas/resources/backups/after_va.tar.gz ./config

# Start sas.servers on: Meta1, Meta2, Meta3, Compute, VA
ss start
```

## After Midtier

Copy Instructions.html from `/opt/sas/config/Lev1/Documents` and perform validation.
    
```sh
# On Midtier
alias ss=/opt/sas/config/Lev1/sas.servers
ss status

# MetaData Backup in SMC
# Backup after SAS Midtier config
/opt/sas/SASHome/SASManagementConsole/9.4/sasmc &

# Server Validations referencing Instructions.html
```


## Post Deployment Config

Reference Course Notes.

- Configuring the SAS/GRAPH Java Applet location
- Restarting the SAS Environment Agents
- Performing a backup with the SAS Deployment Backup and Recovery Tool.
- Create the LASR Administrator Account in Metadata
- Change the Permissions for the LASR Signature and Log Files
- Change the Permissions on the Autoload Directory
- Enable the Autoload Feature for the Public LASR Server



# References

## Directory Locations

- depot - `/SASdepot/SAS9.4M4`
- SASHome - `/opt/sas/SASHome`
- Instructions.html `/opt/sas/config/Lev1/Documents`

## Useful Aliases

```sh
alias depot=cd /SASDepot
alias home=cd /opt/sas/SASHome
alias ss=/opt/sas/config/Lev1/sas.servers
alias dms=/opt/sas/SASHome/DataFluxDataManagementServer/2.7/dmserver/bin/dmsadmin
alias vpo=/opt/sas/SASHome/SASVisualProcessOrchestrationServer/2.1/poserver/bin/dmsadmin
```

## Installing QKBs manually

```
[sas@compute qkb]$ cd /opt/sas
[sas@compute sas]$ gzip -c -d /SASdepot/SAS9.4M4/products/knwldgebseci__94150__lax__xx__sp0__1/qkb-ci-v27-lin64.tar.gz | tar xvf -
[sas@compute sas]$ perl qkb/install.pl
```

TIME THE MIDTIER CONFIG - if longer than 2hours, let proctor know.
