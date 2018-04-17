## Overview

A SAS Environment with SAS Grid Manager takes advantage of:

- higher number of servers available for processing
- strict regulated environment: load is distributed as evenly as possible across the machines

Note: Don’t let in more users that the machines can handle.

## Features (categories)

- **Multi-User Workload Balancing**
    - distributes processing on as many machines as possible. 
    - monitors each machine (cpu, mem, disk, etc…) to know exactly how busy 
    - uses OS commands to get this info to know if the machine is busy due to SAS or something else.
    
Goal: prevent machines running at 100% cpu (not efficient). 

If there are too many processes competing for limiting resources (cpu, mem, io), the machine will spend more time sharing out these resources rather than doing actual work.

- **Parallelized Workload Balancing** 
    - a task (made of sub-tasks) can run in parallel, possibly on different machines.
    - only works if sub-tasks are independent.

- **Distributed Enterprise Scheduling**
    - program (or task) triggered to run on a specific schedule.
    - event triggers include: 
        - time event (3am every weekday)
        - file event (new .csv file appeared)
        - job event (job A finished, kick off job B)
    - the job will be SUBMITTED to grid when trigger, not necessarily run at that time depending on priority

- **High Availability**
    - applies to SAS Services (Metadata, OLAP, Obj Spawner, etc.)
    - can also be used for rolling updates

- **Scalability**
    - can easily add more machines to the env.
    
## Why Grid?

- SAS Users: performance
- IT department: governance, more control over SAS processes, less downtime


    
# SAS Grid Manager (SGM)

## Batch Grid Processing

Execute SAS process in batch on grid (standalone)

- SAS job run on a grid machine (whole time) then disappears after finished
- don't expect user interaction, "fire and forget" 
- uses SASGSUB, Data Step Batch server with Schedule Manager plugin

## Interactive Grid Processing 

Create interactive SAS session on grid, uses **SAS/CONNECT**. 

- SAS session (client) running on grid can request 1+ grid session(s) to be started on grid machines.
- when grid session starts, there are 2+ SAS sessions (running at same time) connected via **SAS/Connect**
- grid session lives while parent is alive, keeps running even if not used 100% of the time
- one session from the client connects to multiple grid sessions 
- not many good use cases, below workload balancing or batch is better

- created using grdsvc_enable and signon SAS statements
- from any machine part of the grid
- using manually written code or automatically by client interface (EM, DI Studio)

## Grid as Load Balancing Algorithm 

Algorithm for load-balancing SAS services: Workspace server, Stored Process, Pool workspace, OLAP server.

- LSF looks at load info of each machine
- Grid-launched servers still managed via objspawn
- Object Spawner acts as a client to get jobs running on grid machines
- multiple connections coming from the client to grid sessions

# Grid Hardware Architecture

## Servers

Compute nodes must be on server-grade machines (not desktops). Best practice is to keep all machines as similar as possible

- software licensing often based on CPU core count
- save on HW costs by using smaller (cheapers) hosts instead of a single large (expensive) server
- lean towards linux / unix variant for grid deployments over Windows

## Shared Storage

Typically a SAN with a dedicated storage technology for fast I/O throughput.

- central location for file storage
- accessible by all grid machines
- minimum throughput capacity: 100-150MB/s/core
- SAN will need a clustered file system
    - handles concurrency of data access, needs read and write
    - not normally supplied, likely requires additional SW purchase (expensive)

**Storage Designs**

Goal: Spread out I/O for availability and performance. 

Examples:

- UTILLOC on dedicated storage: separates SAS threaded procedures from SASWORK
- SASWORK on shared storage: allows for checkpoint/restart (if grid hosts fails mid-job), better parallelization 
(SAS process can "see" other's WORK libraries)

1. Shared SASWORK within Shared Storage with servers having their own dedicated area for SASWORK.
2. Shared SASWORK + Dedicated SASWORK within Shared Storage

Careful cleaup and monitoring of these locations limits downsides.

## Shared File System (required)

- Share for **Platform Suite**: LSF cluster needs single location to store files
    - all machines in cluster need access
    - contains LSF config files
- Share for **Data**: each grid session needs to be able to access same data, same LIBNAME and path
    - don't know which machine the program will run on.
- Share for **SASGSUB**: needs a grid work dir accessibly from clients and grid nodes 
- Share for **other clients**: EG / AMO needs shared area for tmp data
    - path requested by SDW at install time    
- Share for **HA**
- Share for **deployed jobs**: programs need to be stored in central location for all nodes to access.

Because we can assume shared file system in place:

- Share for **LSF binaries** (unix only): install LSF once and use on all grid hosts.
- Share for **SAS binaries** (unix only): install once and use on all grid hosts.
- Share for **SAS Compute Tier config**: config once and use on all grid hosts.

Note: Each Metadata server AND each mid tier requires DEDICATED config directories.

## Networking

- DNS for hostname resolution

LSF provides own hosts file for:

- servers with more than one NIC (multi-homing)
- overriding site-specific DNS/hostname issues

## Virtualization

- easy scaling by cloning nodes
- maintenance/testing: multiple versions of same machines (with / without hotfix)


# Grid Software Architecture

**SAS Software**

    - sasgrid script
    - grdsvc_enable statement
    - sasgsub
    - Env Mgr + SMC plugins
    
** Third Party**: managing/monitoring using Platform Computing (IBM) or Hadoop 

Gives ability to run commands, programs, scripts on any machine 
in the grid and monitor the state of each machine and make scheduling decisions based on that information.

    - LSF
    - Process Manager
    - Grid Monitoring Server
    - Platform Computing Mid-tier Services

## Components

NOTE: BASE SAS and SAS/CONNECT are ALWAYS a pre-requisite.

**SAS Grid Manager Control Server**: two types, for **Platform** or **Hadoop**.

- SAS Grid Manager Control Server OR Grid Manager for Hadoop Control Server
    - SAS Grid Manager Plug-in for SAS Environment Manager OR Grid Manager for Hadoop
    - Platform Web Services for SAS
    - SAS LASR Analytics Server Access Tools
    - SAS TKGrid for Hadoop deployments

For Hadoop Control Server deployments: only runs on Linux, does NOT contain OEM or mid tier components.

**SAS Metadata Server**: Platform LSF for SAS (optional, used for HA) license not available for Hadoop grids.

**SAS Grid Manager Nodes**: Licensed based on total amount of cores for the whole grid.

- SAS Grid Manager setinit
- SAS LASR Analytics Server Access Tools: client components required to transfer data to separately licensed LASR server.
- Platform LSF for SAS 

**SAS Grid ManagerClient (optional)**: Classic Base SAS installed on desktop, 
acts as client who submits code to grid.

**SAS Grid Manager Thin Client**: CLI utility SASGSUB which permits user to submit SAS code to grid.

**SAS Grid Manager Administrative Clients**: not available for Hadoops grids, 
because Hadoop already contains all required mgmt interfaces.


## Platform Suite for SAS (PSS)

IBM Platform Computing components bundled with SAS Grid Manager.

- **Load Sharing Facility (LSF)**: makes up the grid
    - dispatches jobs, monitors, spreads workload, returns status of each job
    - installed on ALL machines in cluster (not required on dedicated metadata or mid tiers)
    - made up of LIM, RES, SBD (daemons)
    - includes EGO (enterprise Grid Orchestrator) responsible for HA features.

- **Process Manager (PM)**: job scheduler for grid.
    - controls submission of scheduled jobs (and manages dependecies between jobs) to grid
    - allows for flows to be created in Schedule Manager or DI studio to run tasks on grid

- **Grid Management Services (GMS)**: provides admin control from SMC (optional)
    - typically installed only on Grid Control server

- **Platform Web Services (PWS)**: used by Env Mgr to report/track/monitor the grid.
    - deployed as SASServer14_1 on the mid tier (along with SAS Grid Mgr Environment Mgr Module)




















