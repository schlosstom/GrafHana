# Some usefull commands mostly used internal  

## Building docker images
```
docker build --tag=hana:latest hana/
```

## Starting GafHANA
```
docker-compose --compatibility up -d

docker-compose --compatibility restart  promtail-ext
```

## Usefull commands within the GrafHANA container
```
# Login into the container
docker exec -it hana01 /bin/bash

# Backup
su - ha0adm
hdbsql -u system -i 00  "BACKUP DATA USING FILE ('myfirstbackup')"

# Log backup
su - ha0adm
hdbcons
> log backup

# Start promtail after is was stopped 
nohup promtail-linux-amd64 -config.file=/etc/promtail/promtail.yml &

# HANA Install command 
cd /src
./hdblcm --dump_configfile_template=templateFile --action install
cat templateFile.xml | ./hdblcm --action=install -b --configfile=templateFile --read_password_from_stdin=xml

```


