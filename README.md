# GrafHana

GrafHana (Grafana and HANA on docker) is a collection of docker containers for developing queries and metrics related to a HANA DB. 

By simply running docker-compose the following container will be created:

  * HANA single DB including promtail (SAP HANA sources are needed)
  * Grafana Loki
  * Grafana Dashboard

# Directory structure

```

  ├── docker-compose.yaml
  ├── grafana
  │   └── var_lib_grafana
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
     ├── (templateFile)
     └── (templateFile.xml)

```

**Important:**

The SAP HANA installation files (DATA_UNITS/HDB_SERVER_LINUX_X86_64) are not included.
They have to download and add separately.

---


# Preparation

  1. Download and unpack the original HANA sources from SAP.

  2. Copy the files to the directory hana (see Directory structure above)
     In our example we only need "DATA_UNIT/HDB_SERVER_LINUX_X86_64/".

  3. Because we install HANA in batch mode we have to create the config files first.
     ./hdblcm --dump_configfile_template=templateFile --action install

  4. Change the file templateFile and templateFile.xml to you needs.
     (examples can be find in the folder example) 

  5. Build the HANA docker container:
     docker build --tag=hana:latest hana/


# Deployment

  Run: docker-compose up -d


# Usage

  * Grafana Dashboard: 
    http://localhost:3000

  * Exec console into HANA: 
    docker exec -it hana01 /bin/bash
    





