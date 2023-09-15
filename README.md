'´'![GrafHana logo](examples/logo01.png)

GrafHana (Grafana and HANA on docker) is a collection of docker containers for developing queries and metrics related to a HANA database. It can also be used for getting familar with Grafana, Prometheus, Loki and Prometheus Alertmanager. And last but not least for getting first experiances on HANA.  

Onced prepared it also can be used for demos running on any Laptop with at least 16 GB of memory.


# Why GrafHana:

  * Easy to deploy within 15 min.
  * A Laptop with >= 16 GB of RAM is enough.
  * CPUs are limited to 2.5 to not decrease the overall performance of the system to much. 
  * All Grafana components are already pre-configured and can be used out of the box.
  * The HANA installation of GrafHana is using template files for the installation. No interaction during the installation is needed at all.  
  * Beside docker, no other special packages are needed on the system.
  * Persistent volumes are used to not lose any data. (Grafana components only) 


**Please note that HANA running inside the container was never created and tested for productive use at all.** These container has only a extreamly minimum of packages to get the HANA started. It also limits CPU usage and memory to a absolute minimum. 


# Containers

  * hana01 
      * hana 2.0 (HANA installation source is needed - see below)
      * promtail
      * node-exporter
      * blackbox-exporter
      * grafana loki

  * prometheus
      * grafana prometheus
   
  * loki
      * grafana loki 

  * grafana
      * grafana dashboard

  * alertmanager
      * alertmanager for e.g. email notify 


# Project structure (shortened)

```
├── conf
│   ├── grafana.ini
│   ├── loki
│   │   ├── loki.yaml
│   │   └── rules
│   │       └── rules.yml
│   ├── alertmanager.yml
│   ├── alertmanager-template.yml
│   ├── blackbox.yaml
│   ├── prometheus
│   │   ├── prometheus-rules.yml
│   │   └── prometheus.yml
│   └── promtail.yml
├── docker-compose.yaml
├── Dockerfile
├── examples
│  [...] 
│   ├── commands.md
│   ├── logo01.png
│   ├── templateFile
│   └── templateFile.xml
├── grafana
│   └── data
│       └── grafana.db
├── hana
│   └── (DATA_UNITS)
│        └── (HDB_SERVER_LINUX_X86_64)
│            [...]
│             ├── (templateFile)
│             └── (templateFile.xml)
└── README.md
```

**Important:**  
The SAP HANA installation files (DATA_UNITS/HDB_SERVER_LINUX_X86_64) are not included. They have to download and add separately.

---


# Preparation

  **To run all the container you need at least 16 GB RAM and about 50 GB of diskspace.**

  1. Clone the repository: ```git clone https://gitlab.suse.de/tschloss/grafhana.git```

  2. Download and unpack the original HANA sources from SAP.

  3. Copy or symlink the HANA source files to the directory hana/ (see Directory structure above)  
     In our example we only need ```DATA_UNIT/HDB_SERVER_LINUX_X86_64``` (it is about 4G). 
   
  4. Because we install HANA in batch mode we need two templateFiles:

        * ```templateFile.xml``` containing the HANA configuration.
        * ```tempateFile``` where all the passwords are stored.

     Already prepared example files can be find in the folder [example](examples/)  
          
     These files has to be placed under ```hana/DATA_UNIT/HDB_SERVER_LINUX_X86_64```   
     .

          If you like to create these template files from scratch the following command is needed: 
           ./hdblcm --dump_configfile_template=templateFile --action install


  5. Edit and rename the file **alertmanager-template.yml** to you needs.


# Deployment

  Run:  
  **docker-compose --compatibility up -d**
 

# Usage

  * Grafana Dashboard: 
    http://localhost:3000

  * Grafana Prometheus: 
    http://localhost:9090

  * Alertmanager: 
    http://localhost:9093

  * Exec into HANA:  
  ```docker exec -it hana01 /bin/bash```

  * Progress of the HANA installation can be checked with:  
    ```docker logs -f hana01```

  * Stop the whole stack   
	  ```docker-compose --compatibility down```   
    The grafana config (including Dashboards, grafana.ini, etc) are persistent after stopping.   
    **Any  HANA DB changes will get lost.**
  
Default password for HANA is **Suse1234**


# Details

#### Log Aggregation
GrafHana is currently collecting the following files whith Promtail:

 *  backup logs
 *  indexserver traces
 *  nameserver traces
 *  system_availability traces
 *  /var/log/messages from the docker host

#### Monitoring
On deploying GrafHana, Prometheus and its node-exporter is running out of the box. There is also a Node Exporter Dashboard available.

#### Alerting
Prometheus and Loki Alerts vial Alertmanager are possible. (see [Using Alerts](#using-alerts))


# Usefull Tips

#### Run GrafHana with an already installed HANA
On default, if you run GrafHana the HANA DB will always installed from scratch. Installing takes about 15 min. It is however possible to take a "screenshot" from the current running container with the command ```docker commit```. This will create a image copy of the current state of the hana container. 

The following steps are needed to get it permanent running with docker-compose:

1. Run the first time ```docker-compose --compatibility up -d``` like describe above.  
2. Wait until HANA is complete install.  
3. Run the command: ```docker commit hana01 hana2``` to get a new docker image.  
4. Stop the containers with ```docker-compose --compatibility down```  
5. Change the following lines in **docker-compose.yaml**:  
			1. ```image: hana:latest``` to ```image: hana2:latest```  
			2. ```cd /src && cat templateFile.xml | ./hdblcm --action=inst....``` to ```su - ha0adm HDB start```
6. Run ```docker-compose --compatibility up -d```
				
**Note** Please keep in mind that the HANA licence might expire after some weeks. 

#### Starting GrafHana without HANA
It is sometimes usefull to not start/install the HANA DB but instead starting only all the Grafana tools. I use this often if I for example only want to troubleshoot my promtail or loki configuration. The latest version of the docker-compose.yaml file has the whole HANA Install line outsoureced to the env variable ```$HANA```. 
There is now a new enviroment file named ".env-noHANA" which can be used with the following command:

``` docker-compose --compatibility --env-file .env-noHANA up -d ```

Because these new file has ```HANA=""``` in it the HANA DB will not started.


#### <a name="using-alerts"></a>Using Alerts
Alerts can configured in different way's and for different sources. GrafHana is currently able to 
use Prometheus and Loki rules. there is a template/example for each rule type available:

* Prometheus rule file: ```conf/prometheus/rules/rules.yml```
* Loki rule file: ```conf/loki/rules/fake/rules.yaml ```
 
 For both way's the prometheus alertmanager will be used to manage the alert's.
 There is also a template alertmanager yaml file avaiable:

 * ```alertmanager-template.yml``` 

It can be changed to you needs and has to be renamed to ```alertmanager.yml```.



#### Using external logs with GrafHANA

This feature has been moved to a separate project.   
Please see new repository called: [Grafhista](https://github.com/schlosstom/GrafHista).


# Changelogs

* 2023-08-15 - Move GrafHana to GitHub under the GPL License
* 2023-08-11 - add alertmanager template file.
* 2023-08-10 - Ingegrate the blackbox exporter.
* 2023-08-10 - Loki alerts are now possible. 
* [....]
