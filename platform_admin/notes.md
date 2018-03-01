## Backups

## SAS Environment Manager

- Database stored on **compute** tier's **Web Infrastructure Platform (WIP)** server.
- Agents running on each server reports back to Environment Manager server on different intervals.

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

# Metadata

## Repositories

3 Types

- **Foundation** repository: required metadata store, cannot have more than one.
- **Custom** repositories: used for physically separating metadata for storage or security. (special use cases)
- **Project** repositories: isolated work area for SAS Data Integration Studio (very special use cases)

Note: When the first query for a specific type of metadata object (for example, a table) is submitted, all
table metadata is loaded into memory. The in-memory database remains until the metadata server is
paused or stopped.

## Journaling

- Enabled by default
- Contains every transaction made to metadata in THAT DAY
- New file every day 
- old file backed up 1AM server time by default
- `JOURNALTYPE=ROLL_FORWARD` creates a linear file storing all transaction occurred since the most recent backup.

## Start Up

- Reads `omaconfig.xml` file at start-up. (OMA = open metadata architecture) Contains: 
    - location of repo manager
    - email address, where to send alert emails
    - journaling options

Any changes to this file requires a restart of the metadata server.

# Object Spawner

- Reads `metaconfig.xml` file at start-up.


## Logins

Inbound logins, only fully qualified name required.

Outbound, Authdomain, keyid, pass.
