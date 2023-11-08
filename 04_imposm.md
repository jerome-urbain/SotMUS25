

# <span style="color:darkblue">Imposm<span>

Official website: https://imposm.org/docs/imposm3/latest/

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

## <span style="color:darkblue">Import OSM data from an osm.pbf file based on mapping rules<span>

By default the OSM data import using imposm is divided in 2 steps:
- Reading
- Writing



### <span style="color:darkblue">Reading<span>


 > Building the way and relation geometries requires random access to all nodes and ways, but this is not supported by the OSM PBF data format. Imposm needs to store all nodes, ways and relations in an __intermediary data store that allows random access to these elements__. It does this __on-disk to keep the memory usage of Imposm low__. Having lots of memory will still speed the import up, because your OS will use all free memory for caching of these files. Imposm uses LevelDB key-value databases for this, which are fast and compact.

Imposm needs to know which OSM elements you want to have in your database. You can use the provided mapping.yml file for this tutorial, but you should read Data Mapping for more information on how to define your own mapping.

Example: reading belgian data based on a ```mapping.yml``` file:
```bash
imposm import -mapping mapping.yml -read belgium.osm.pbf
```

We can see that a ```-read``` parameter is used in the command.


### <span style="color:darkblue">Writing<span>

> The second step is the writing of OpenStreetMap features into the database. It reads the features from the cache from step one, builds all geometries and imports them into the according tables. By default it overwrites existing tables.

Example: write data already cached in a Postgis database having a dedicated osm schema:
```bash
imposm import -mapping mapping.yml -write -connection postgis://user:password@localhost/osm_schema
```
We can see this time that a ```-write``` parameter is used in the command.

### <span style="color:darkblue">Combine reading and writing<span>

It is also possible to combine reading and writing in one single step:

```bash
imposm import -mapping mapping.yml -read belgium.osm.pbf -write -connection postgis://user:password@localhost/osm_schema
```

Both ```-read``` and ```-write``` parameters are use in this configuration.

