# GrafHana

GrafHana (Grafana and HANA on docker) is a collection of docker containers for developing queries and metrics related to a HANA DB. 
The underlying system should have at least 16G of RAM and about 50 GB disk space. CPU for the HANA docker container is limited 
to 2.5 CPUs to avoid any high CPU pressure of the whole system (see docker-compose.yaml:limits: cpus: '2.5')

Running docker-compose the following container will be created:

  * HANA single DB including promtail (SAP HANA sources are needed - see describtion below)
  * Grafana Loki
  * Grafana Dashboard

# Directory structure

```

  ├── docker-compose.yaml
  ├── grafana
  │   ├── data
  │   └── etc
  ├── hana
  │   ├── (DATA_UNITS)
  │   │   └── (HDB_SERVER_LINUX_X86_64)
  │   ├── Dockerfile
  │   ├── promtail
  │   │   └── config.yaml
  │   └── start.sh
  ├── loki
  │      ├── data
  │      └── loki.yaml
  └──examples  
     ├── commands.txt
     ├── templateFile
     └── templateFile.xml

```

**Important:**  
The SAP HANA installation files (DATA_UNITS/HDB_SERVER_LINUX_X86_64) are not included.
They have to download and add separately.

---


# Preparation

  0. The system to run all the container needs at least 16 GB RAM and about 50 GB ofdiskspace.

  1. Download and unpack the original HANA sources from SAP.

  2. Copy the files to the directory hana (see Directory structure above)  
     In our example we only need **DATA_UNIT/HDB_SERVER_LINUX_X86_64/** (it is about 4G).

  3. Because we install HANA in batch mode we have to create the config files first.  
     **./hdblcm --dump_configfile_template=templateFile --action install**  
     The file templateFile and templateFile.xml can be changed to you needs.    
     An example can be found in the folder example. 

  4. Build the HANA docker container:  
     **docker build --tag=hana:latest hana/**


# Deployment

  Run:  
  **docker-compose --compatibility up -d**

  The progress of the HANA installation can be checked with:  
  **docker logs -f hana01**


# Usage

  * Grafana Dashboard: 
    **http://localhost:3000**

  * Exec console into HANA:   
    **docker exec -it hana01 /bin/bash**
    





