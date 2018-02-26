## Backups

### Environment Snapshot

## Hotfixes

**SASHFADD**

- generates a report which can be downloaded locally
- download all (or some) hotfixes using report
- transfer hotfixes to server
- apply with SDM

## Startup Params

Enter updates/additions in `sasv9_usermods.cfg` instead of `sasv9.cfg`. 
This is to preserve your changes, since the original sasv9.cfg file may be overriden later automatically.


**SAS Deployment Agent** doesn't automatically start on UNIX (by default). To start and stop use:

- SAS Deployment Manager
- SAS Environment Manager
- command located in SASHome
