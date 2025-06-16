# <span style="color:#66C3FF">Postgis + imposm docker stack<span>

To enable the Postgis container and the Imposm container to communicate with each other, we're going to place them in the same docker stack, created from a ```docker-compose.yml``` file.


In a Docker Compose setup, when containers are defined within the same service stack, they __share the same network namespace by default__. 
This means they can communicate with each other using their service names as hostnames. 
For example, if we include services named ```imposm``` and ```postgis``` in a ```docker-compose.yml``` file, the imposm service can connect to the postgis db service using the hostname "postgis".
This visibility between containers in the same stack simplifies inter-container communication, making it easier for services within the same application stack to interact with each other seamlessly.


To combine Postgis and Imposm in a stack, you can save the following ```docker-compose.yml``` file:

```yaml
version: '3.8'

services:

  postgis_osm:
    image: postgis/postgis:17-3.5
    ports: 
      - "5482:5432"
    environment:
      - POSTGRES_PASSWORD=postgres
      
  imposm:
    image: geopostcodes/imposm:1.2
    stdin_open: true  # Equivalent to -i
    tty: true         # Equivalent to -t
    volumes:
        - ./osm_data/:/osm_data   
        - ./mapping/:/mapping
    depends_on:
        - postgis_osm
    deploy: 
        restart_policy:
            condition: on-failure
            delay: 3s
            max_attempts: 5
            window: 5s
```

and, in the folder where you stored it, run the following docker command:
```bash
docker-compose up
```

We now have the following infrastructure:
<img src="../figures/infra.drawio.png" alt="Infrastructure: PostGIS & Imposm" width="500" style="display: block; margin: 0 auto">



```{note}
IMPORTANT NOTE:
As the 2 containers are in the same stack and have simplified inter-container communication, you can use the port 5432 (and not 5482) and the service names 'postgis_osm' as hostname when you want to reach postgis database from imposm container.
```

You can now enter the container bash terminal by typing the following command:
```bash
docker exec -it imposm-imposm-1 bash
```

We will run some commandline from this terminal, you can keep it open for now!



## <span style="color:#66C3FF">Import OSM data from an osm.pbf file based on mapping rules<span>

By default an OSM data import process using Imposm is divided in 2 steps:
1. Reading step
2. Writing step



### <span style="color:#66C3FF">Reading<span>


 > Building the way and relation geometries requires random access to all nodes and ways, but this is not supported by the OSM PBF data format. Imposm needs to store all nodes, ways and relations in an __intermediary data store that allows random access to these elements__. It does this __on-disk to keep the memory usage of Imposm low__. Having lots of memory will still speed the import up, because your OS will use all free memory for caching of these files. Imposm uses LevelDB key-value databases for this, which are fast and compact.
Source: https://imposm.org/docs/imposm3/latest/tutorial.html

Imposm needs to know which OSM elements you want to have in your database. You can use the provided mapping.yml file for this tutorial, but you should read Data Mapping for more information on how to define your own mapping.

Example: to reading Andorra OSM data based on a ```mapping.yml``` file, You can run the following command from the terminal inside the Imposm docker container:
```bash
# if you are not running a terminal session in the imposm container, run also the next line:
# docker exec -it imposm-imposm-1 bash
/usr/app/imposm import -mapping /mapping/mapping.yml -read /osm_data/andorra-latest.osm.pbf
```

We can see that a ```-read``` parameter is used in the command and the ```mapping.yml``` is used to load the mapping rules between OSM files and Postgresql database.


### <span style="color:#66C3FF">Writing<span>

> The second step is the writing of OpenStreetMap features into the database. It reads the features from the cache from step one, builds all geometries and imports them into the according tables. By default it overwrites existing tables.
Source: https://imposm.org/docs/imposm3/latest/tutorial.html

Example: write data already cached in a Postgis database and specify the destionation database (postgres):
```bash
# docker exec -it imposm-imposm-1 bash
/usr/app/imposm import -mapping /mapping/mapping.yml -write -dbschema-import osm -connection postgis://postgres:postgres@postgis_sotm:5432/postgres
```
We can see this time that a ```-write``` parameter is used in the command and we need to specify the connection string to access the Postgis database. In addition, a ```-dbschema-import``` parameter is used to specify the database schema (osm).

### <span style="color:#66C3FF">Combine reading and writing<span>

It is also possible to combine reading and writing in one single step:

```bash
# docker exec -it imposm-imposm-1 bash
/usr/app/imposm import -mapping /mapping/mapping.yml -read /osm_data/andorra-latest.osm.pbf -write -connection postgis://postgres:postgres@postgis_sotm:5432/postgres
```
Both ```-read``` and ```-write``` parameters are use in this configuration.



If we want to add more parameters to customize the import, we can for example ask for overwriting the cached data during the import, specify the destination database schema and specify the port number:

```bash
/usr/app/imposm import -mapping /mapping/mapping.yml -read /osm_data/andorra-latest.osm.pbf -write -overwritecache -dbschema-import osm -connection postgis://postgres:postgres@postgis_sotm:5432/postgres
```


```{note}
__Note:__ now that you did your first import with success, feel free to change mapping rules, import parameters and ingest bigger countries to get an overview of Imposm capabilities!
```
