SMP - cannot have multiple SMP isntances in a single VA env

SMP (non-distributed) vs MPP (distributed)
https://communities.sas.com/t5/tkb/articleprintpage/tkb-id/library/article-id/1243

# VA SMP Architecture - Software

- Single in-memory machine
- No SAS High Performance Analytics Environment
- No co-located data provider
- non-dist lasr server runs on its own host (not on same host as MID TIER, why? memory, dedicated CPU cycles)

## Integration

Supported on **Linux x64** and **Windows x64** not AIX, etc.

### SAS WIP Data Server

- Relational DB for SAS 9.4
- Platform DB (SharedServices)
- deploys SAS Env Mgr DBs (Administration, EVManager)
- deploys SAS VA DBs

Best Practice: dedicated host for LASR Server
 - prevents other users impacting from LASR, and LASR from impacting users
 - consider location of data and if the data is shared
 
When mid tier struggling after adding many users (large # of concurrent users):
SAS Web Application Tuning Guide

Resources:

http://sww.sas.com/blogs/wp/gate/3161/thinking-about-the-middle-tier-part-1/suksiw/2014/11/14
http://sww.sas.com/blogs/wp/gate/3573/thinking-about-the-middle-tier-part-2/suksiw/2014/12/16

## VA MPP Architecture - Software

- multiple machines with high-perf analytic infra (high-perf analytics environment)
- co-located data prov or remote data prov

Supported:
- Linux x64 supports all (metadata, va server tier, va mid tier, distributed lasr root node, dist lasr worker nodes)
- Windows x64 supports ONLY meta, va serv tier, and va mid tier
- LASR Root Node AND LASR Worker nodes ONLY on Linux x64

SAS Embedded Process - used for parallel loading of data. (can exist on remote data prov)

Integration: same as VA SMP.

Licensing:
 - # cores required for MPP.
 - Can have SMP and MPP licensed together.
 
 ## HPAI Architecture - Software
 
 Contains:
 
 - LASR 
 - TKGrid
 - HPAI
   - Root Node
   - Worker Nodes
  
 Supported Third-party Haddoop (we don't prov Hadoop anymore): NameNode, DataNodes.
 
 Hadoop Distributions
 
 - Apache Hadoop (open-source), not recommended for production
    - Requires config with SAS Plug-in for Hadoop
    - No HA for NameNode
 - Others: Cloudera, Hortonworks HDP 2.6, etc.
 
 
