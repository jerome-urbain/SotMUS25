

# <span style="color:darkblue">Imposm<span>

> Official website: https://imposm.org/docs/imposm3/latest/

Imposm is a command-line tool used for __importing OpenStreetMap (OSM) data into PostGIS__ databases efficiently. It is specifically designed to handle large-scale geospatial datasets. Imposm performs tasks such as data filtering, simplification, and schema mapping, ensuring that the data is structured appropriately for storage in a PostGIS-enabled PostgreSQL database. Imposm's command-line interface allows users to customize the import process according to their specific requirements, making it a valuable tool for efficiently importing and managing vast volumes of OSM data for geospatial applications.



```{image} ./figures/imposm_process.png
:alt: imposm
:width: 500px
:align: center
```

## <span style="color:darkblue">What about other OSM data import tools?<span>

In our context, Imposm stands out as the ideal choice for importing OpenStreetMap (OSM) data into PostGIS based on several key advantages when compared to its competitors:

- Unlike __Overpass API__, Imposm avoids the complex syntax, timeout issues, and network dependency. 
- Unlike __Pyrosm__, which loads data into memory, making it suitable for small datasets but impractical for larger ones, Imposm is optimized for importing large OSM datasets into databases. 
- In contrast to __Osm2pgsql__, Imposm offers built-in support for parallel processing, allowing for faster imports, and provides a flexible mapping schema that can be customized according to specific needs. While Osm2pgsql lacks support for incremental updates, Imposm enables the incorporation of changes over time. 

Moreover, Imposm allows for the utilization of Spatial SQL, enabling powerful spatial queries and analysis within the PostGIS environment, providing a comprehensive and efficient solution for handling OSM data at scale.


```{image} ./figures/osm_data_import.png
:alt: osm_data_import
:width: 500px
:align: center
```

## <span style="color:darkblue">Folder structure<span>

## <span style="color:darkblue">Download data<span>

You can download data in osm.pbf format directly from geofabrik website ( http://download.geofabrik.de/ ). I suggest to start with a small country in the following list:
- Andorra: http://download.geofabrik.de/europe/andorra-latest.osm.pbf
- Luxembourg: http://download.geofabrik.de/europe/luxembourg-latest.osm.pbf
- Belgium: http://download.geofabrik.de/europe/belgium-latest.osm.pbf

## <span style="color:darkblue">Filter data during the import with a mapping file<span>

When importing OSM data into PostGIS with Imposm, the ```mapping.yml``` file plays a crucial role. It is used to __define the mapping between OSM data elements (nodes, ways, and relations) and the database schema in PostGIS__. 
Imposm uses this mapping file to determine how OSM data should be stored and organized within the PostGIS database. 

This configuration file contains rules about:

- __Data Transformation__: The ```mapping.yml``` file defines how OSM tags are transformed into database columns. Each OSM tag can be mapped to a specific column in one or more database tables. For example, OSM tags like "highway" or "name" can be mapped to corresponding columns in the database schema.

- __Schema Definition__: It defines the structure of the PostGIS database schema. For instance, it specifies which tables should be created for nodes, ways, and relations, and what columns these tables should contain. The file also defines the data types for each column (e.g., text, integer, geometry).

- __Geometry Types and Coordinates__: For spatial data like points, lines, and polygons, the ```mapping.yml``` file specifies how OSM node coordinates are transformed into geometries. It defines the geometry type (Point, LineString, Polygon) and the coordinate columns (usually "lon" and "lat" for longitude and latitude).

- __Relations__: OSM data often contains relationships between elements. The ```mapping.yml``` file defines how these relations should be represented in the database, allowing for the creation of tables to store these relationships.

- __Advanced Mapping__: The file supports more advanced configurations, such as applying filters based on OSM tags, conditional mapping (mapping certain tags only if certain conditions are met), and handling complex geometries.

By providing a clear and customizable mapping between OSM data and the database schema, the ```mapping.yml``` file allows Imposm to accurately import OSM data into PostGIS, ensuring that the data is properly structured, organized, and ready for spatial queries and analysis within the PostgreSQL database.


Here is a simple mapping file directly inspired by the official imposm3 documentation (https://imposm.org/docs/imposm3/latest/mapping.html).
This configuration allows to creates a ```road``` table in PostGIS to store linear geometries (linestring) related to any OSM data tagged as ```highway``` (of any type).

```yaml
tables:
  road:
    type: linestring
    mapping:
      highway: [__any__]
      #highway: [path, track, unclassified]
    columns:
    - {name: osm_id, type: id}
    - {name: geom, type: geometry}
    - {key: name, name: street_name, type: string}
    - {key: bridge, name: is_bridge, type: bool}
    - {name: highway_type, type: mapping_value}
```

Note that some mapping rules are applied on attributes to define the columns of the ```road``` table:
- a column named ```osm_id``` of type ID is created to store the unique identifier of the OSM element.
- a column named ```geom``` of type geometry is created to store the LineString geometries.
- a ```street_name``` column of type string is created and mapped from the OSM tag with key ```name```. This allows storing street names.
- a column named ```is_bridge``` of type _boolean_ is created and mapped from the OSM tag with key ```bridge``` to indicate whether the road is a bridge or not.
- a column named ```highway_type``` of a custom type _mapping_value_ is created to stores the specific values of the ```highway``` tag (_path, track, unclassified, etc._).

You can also see that there is a commented line to show you what would be this line for specific highway types such as _path, track and unclassified_. If you want to use this filter on highway types you can replace the previous line (containing ```__any__```) with it.


Note that an OSM element is only inserted once even if a mapping matches multiple tags.



## <span style="color:darkblue">Imposm docker image<span>

To skip the imposm installation only available on linux, you can directly pull an existing imposm docker image released by GeoPostcodes team:
```bash
docker pull geopostcodes/imposm:1.2
```

With this docker image you have a ready to use imposm setup in only few seconds! It's pretty cool, isn't it?

```{warning}
IMPORTANT NOTE:
If we instantiate this image and create a container based on it, the container will not be able to communicate with the postgis container we created before because they are isolated by default (container principle). They would not be in the same network. 
In consequence, they cannot communicate together and you cannot reach postgis container from the imposm one, which makes OSM data import impossible based on this setup. Be patient, we will solve this issue in the next section!
```


## <span style="color:darkblue">Postgis + imposm docker stack<span>

To enable the Postgis container and the Imposm container to communicate with each other, we're going to place them in the same docker stack, created from a ```docker-compose.yml``` file.


In a Docker Compose setup, when containers are defined within the same service stack, they __share the same network namespace by default__. 
This means they can communicate with each other using their service names as hostnames. 
For example, if we include services named ```imposm``` and ```postgis``` in a ```docker-compose.yml``` file, the imposm service can connect to the postgis db service using the hostname "postgis".
This visibility between containers in the same stack simplifies inter-container communication, making it easier for services within the same application stack to interact with each other seamlessly.


To combine Postgis and Imposm in a stack, you can save the following ```docker-compose.yml``` file:

```yaml
version: '3.7'

services:

  postgis_osm:
    image: postgis/postgis:16-3.4
    ports: 
      - "5482:5432"
    environment:
      - POSTGRES_PASSWORD=postgres
      
  imposm:
    image: geopostcodes/imposm:1.2
    stdin_open: true  # Equivalent to -i
    tty: true         # Equivalent to -t
    volumes:
        - ./imposm/osm_data/:/osm_data   
        - ./imposm/mapping/:/mapping
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
```{image} ./figures/infra.drawio.png
:alt: infra
:width: 500px
:align: center
```


```{note}
IMPORTANT NOTE:
As the 2 containers are in the same stack and have simplified inter-container communication, you can use the port ```5432``` (and not 5482) and the service names ```postgis_osm``` as hostname when you want to reach postgis database from imposm container.
```

You can now enter the container bash terminal by typing the following command:
```bash
docker exec -it sotm_workshop_imposm_1 bash
```

We will run some commandline from this terminal, you can keep it open for now!



## <span style="color:darkblue">Import OSM data from an osm.pbf file based on mapping rules<span>

By default an OSM data import process using Imposm is divided in 2 steps:
1. Reading step
2. Writing step



### <span style="color:darkblue">Reading<span>


 > Building the way and relation geometries requires random access to all nodes and ways, but this is not supported by the OSM PBF data format. Imposm needs to store all nodes, ways and relations in an __intermediary data store that allows random access to these elements__. It does this __on-disk to keep the memory usage of Imposm low__. Having lots of memory will still speed the import up, because your OS will use all free memory for caching of these files. Imposm uses LevelDB key-value databases for this, which are fast and compact.
Source: https://imposm.org/docs/imposm3/latest/tutorial.html

Imposm needs to know which OSM elements you want to have in your database. You can use the provided mapping.yml file for this tutorial, but you should read Data Mapping for more information on how to define your own mapping.

Example: to reading belgian OSM data based on a ```mapping.yml``` file, You can run the following command from the terminal inside the Imposm docker container:
```bash
# if you are not running a terminal session in the imposm container, run also the next line:
# docker exec -it sotm_workshop_imposm_1 bash
imposm import -mapping /mapping/mapping.yml -read belgium.osm.pbf
```

We can see that a ```-read``` parameter is used in the command and the ```mapping.yml``` is used to load the mapping rules between OSM files and Postgresql database.


### <span style="color:darkblue">Writing<span>

> The second step is the writing of OpenStreetMap features into the database. It reads the features from the cache from step one, builds all geometries and imports them into the according tables. By default it overwrites existing tables.
Source: https://imposm.org/docs/imposm3/latest/tutorial.html

Example: write data already cached in a Postgis database and specify the destionation database (postgres):
```bash
# docker exec -it sotm_workshop_imposm_1 bash
imposm import -mapping /mapping/mapping.yml -write -connection postgis://postgres:postgres@postgis_osm:5432/postgrespostgres
```
We can see this time that a ```-write``` parameter is used in the command and we need to specify the connection string to access the Postgis database.

### <span style="color:darkblue">Combine reading and writing<span>

It is also possible to combine reading and writing in one single step:

```bash
# docker exec -it sotm_workshop_imposm_1 bash
imposm import -mapping /mapping/mapping.yml -read /import/belgium.osm.pbf -write -connection postgis://postgres:postgres@postgis_osm:5432/postgres
```
Both ```-read``` and ```-write``` parameters are use in this configuration.



If we want to add more parameters to customize the import, we can for example ask for overwriting the cached data during the import, specify the destination database schema and specify the port number:

```bash
/usr/app/imposm import -mapping /mapping/mapping.yml -read /import/andorra-latest.osm.pbf -write -overwritecache -dbschema-import osm -connection postgis://postgres:postgres@postgis_osm:5432/postgres
```
