# Preinstall Reference File

- SAS Installer USER/Pass, verify sudo access
- Root Pass
- Server Names

- Credentials
    - sasadm@saspw
    - sastrust@saspw
    - sasdemo

# Plan

Build Reference file.
Setup MobaXTerm with sessions, verify login, sudo access, write permissions to /SASBackups/Metadata

```sh
# Run on each Meta node prior to install
sudo whoami
netstat -a | grep 8561
ls -ld /SASBackups/Metadata

# Only on Meta
touch /SASBackups/Metadata/meta.confirm.rc
```

## Meta Install/Config + Meta2 Install

Validate Meta Config, copy instructions.html, run Metadata backup in SMC

```sh
# On Meta
alias ss=/opt/sas/config/Lev1/sas.servers
ss status

netstat -a | grep 8561

# MetaData Backup in SMC
/opt/sas/SASHome/SASManagementConsole/9.4/sasmc &
```

## Meta2 Config + Meta3 Install
- Validate Meta2 Config, copy instructions.html, run Metadata backup in SMC

```sh
# On Meta2
alias ss=/opt/sas/config/Lev1/sas.servers
ss status

netstat -a | grep 8561

# MetaData Backup in SMC
/opt/sas/SASHome/SASManagementConsole/9.4/sasmc &
```

## Meta3 Config + Copy JUnit -> Compute Install

```sh
mkdir -p /opt/sas/thirdparty/JUnit
cp /SASdepot/ThirdParty/JUnit/junit-4.8.1.jar /opt/sas/thirdparty/JUnit/
```

After Meta3 config pass copy Instructions.html from `/opt/sas/config/Lev1/Documents` and perform validation.

```sh
# On Meta3
alias ss=/opt/sas/config/Lev1/sas.servers
ss status

netstat -a | grep 8561

# MetaData Backup in SMC
/opt/sas/SASHome/SASManagementConsole/9.4/sasmc &

# if it doesn't exist
mkdir /opt/sas/resources/backups

# Stop sas.servers on: Meta3, Meta2, Meta1
ss stop

cd /opt/sas
sudo tar -cvzpf /opt/sas/resources/backups/after_cluster.tar.gz ./config

# Start sas.servers on: Meta1, Meta2, Meta3
ss start
```

## Compute Config, VA Install

After computer config pass copy Instructions.html from `/opt/sas/config/Lev1/Documents` and perform validation.

```sh
# On Compute
alias ss=/opt/sas/config/Lev1/sas.servers
ss status

# MetaData Backup in SMC
/opt/sas/SASHome/SASManagementConsole/9.4/sasmc &

# Stop sas.servers on: Compute, Meta3, Meta2, Meta1
ss stop
 
cd /opt/sas
sudo tar -cvzpf /opt/sas/resources/backups/after_compute.tar.gz ./config

# Start sas.servers on: Meta1, Meta2, Meta3, Compute
ss start
```

## VA Config, Midtier Install

After VA config pass copy Instructions.html from `/opt/sas/config/Lev1/Documents` and perform validation.
    
```sh
# On VA
alias ss=/opt/sas/config/Lev1/sas.servers
ss status

# MetaData Backup in SMC
/opt/sas/SASHome/SASManagementConsole/9.4/sasmc &

# Stop sas.servers on: VA, Compute, Meta3, Meta2, Meta1
ss stop
 
cd /opt/sas
sudo tar -cvzpf /opt/sas/resources/backups/after_va.tar.gz ./config

# Start sas.servers on: Meta1, Meta2, Meta3, Compute, VA
ss start
```

## Midtier config

After midtier config pass copy Instructions.html from `/opt/sas/config/Lev1/Documents` and perform validation.
    
```sh
# On Midtier
alias ss=/opt/sas/config/Lev1/sas.servers
ss status
```

# Detailed Validations

## After Metadata Cluster
## After Compute
## After VA
## After Midtier
    

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

## Backup after each config pass

```sh
# if it doesn't exist
mkdir /opt/sas/resources/backups

cd /opt/sas
sudo tar -cvzpf /opt/sas/resources/backups/DESCRIPTION.tar.gz ./config
```

## Installing QKBs manually

```
[sas@compute qkb]$ cd /opt/sas
[sas@compute sas]$ gzip -c -d /SASdepot/SAS9.4M4/products/knwldgebseci__94150__lax__xx__sp0__1/qkb-ci-v27-lin64.tar.gz | tar xvf -
[sas@compute sas]$ perl qkb/install.pl
```

TIME THE MIDTIER CONFIG - if longer than 2hours, let proctor know.
