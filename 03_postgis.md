# <span style="color:darkblue">Postgis<span>

------------
## <span style="color:darkblue">Storing geographic data in Postgresql: Postgis to the rescue!<span>
------------

PostGIS is an open-source geospatial extension for PostgreSQL, a popular relational database management system (RDBMS). 
It enhances PostgreSQL by adding __support for geographic objects__, allowing the storage, retrieval, and analysis of geospatial data. 
PostGIS enables the database to handle various geospatial data types such as points, lines, polygons, and geographic features with associated attributes. It provides a __wide range of spatial functions and operators__, enabling __complex geospatial operations__ like spatial joins, buffering, and geometric calculations. PostGIS is powerful because it allows users to store and manage large volumes of geospatial data efficiently, perform spatial queries for mapping applications, conduct spatial analysis, and support geographic information system (GIS) functionalities within a database environment. Its ability to handle __spatial indexing__ and __advanced spatial analysis__ makes it a valuable tool for applications dealing with geospatial data.

```{image} ./figures/postgis_logo.png
:alt: postgis
:width: 600px
:align: center
```

Regarding OpenStreetMap data, which can be massive at the country scale, PostGIS supports efficient __spatial indexing__, enabling fast searches and complex geospatial queries. Its optimized spatial indexing system ensures high performance even with a significant volume of data, which is essential for some applications.

Moreover, PostGIS provides a variety of spatial functions such as __distance queries__, __spatial joins__, __advanced geometric operations__, and more. These functionalities are crucial for analyzing geospatial data-driven applications.

Lastly, the choice of PostGIS is reinforced by its __open source nature and active community__. The PostGIS community provides regular updates, security patches, and comprehensive documentation, making it a reliable choice for storing and querying medium to large-scale OpenStreetMap datasets.

------------
## <span style="color:darkblue">Postgis docker container<span>
------------

Now that you both know Docker and Postgis it is time to use them!

To directly create a Postgresql 16 database including Postgis extention (version 3.4), you can run the following command:

```bash
docker run \
    --name postgis_sotm \
    -p 5452:5432 \
    -e POSTGRES_PASSWORD=postgres \
    -d postgis/postgis:16-3.4
```

```{warning}
WARNING: if you are using Mac OS, you can have trouble with this image as it doesn't support ARN64 architecture. You can replace the last line by:

```-d imresamu/postgis:latest```
```


Note that this command include some parameters:

- ```--name postgis_sotm``` specify the name of your new docker container as "postgis_sotm"

- ```-p 5452:5432``` setup the port forwarding. The first port number correspond to your host port and the second port number correspond to your container port. They are different to avoid conflict with an existing Postgresql installation on your device.

    - _Host port number_: to avoid conflict with an existing Postgresql installation running on port 5432 we change this parameter using ```-p 5452:5432``` to setup your container on host port number 5452.
    - _Container port number_: 5432 is the default port of PostgreSQL and you need to expose it to be able to access the database in your container. You can keep this value even if you have another PostgreSQL installation or container.

- ```-e POSTGRES_PASSWORD=postgres``` specify the password for admin user postgres. In this workshop we will only setup a sandbox to play with PostGIS and use postgres/postgres as credentials. If you consider using this container for another purpose please change this setup.

- ```-d postgis/postgis:16-3.4``` specify the docker image from dockerhub we use. Here we use the official PostGIS image ( https://hub.docker.com/r/postgis/postgis ) based on postgres image and in a specific version: Postgresql 16 + Postgis 3.4.


Once your container is running, you can see it in the active container list in Docker Desktop interface or by using the following docker command:

```bash
docker ps
```


------------
## <span style="color:darkblue">Interacting with Postgis<span>
------------

### <span style="color:darkblue">SQL client<span>


To interact with your database, I recommend 3 differents applications with GUI and embedded SQL editor: _DBeaver_, _pgAdmin_ or _QGIS_. To go finish this workshop, you will need at least __one of them__. You could also run SQL command in _psql_ command line tool but it is less user friendly in this learning context. 

#### <span style="color:darkblue">DBeaver community edition (CE)<span>
> DBeaver Community is a free cross-platform database tool for developers, database administrators, analysts, and everyone working with data. It supports all popular SQL databases like MySQL, MariaDB, PostgreSQL, SQLite, Apache Family, and more.

DBeaver _community edition_ is the software I recommend to run SQL queries during this workshop. To install it, you can follow the instructions on their official website:
https://dbeaver.io/download/

To create your first PostgreSQL connexion you can follow the documentation: https://dbeaver.com/2022/03/03/how-to-create-database-connection-in-dbeaver/ . 
Note that DBeaver will automatically download and install PostgreSQL driver when you will create your first PostgreSQL connexion. 

If you followed the previous docker configuration (user, password, port number), you can use the following connection setup:

```{image} ./figures/dbeaver_connection.png
:alt: dbeaver_connection
:width: 500px
:align: center
```




#### <span style="color:darkblue">pgAdmin<span>
> pgAdmin is the most popular and feature rich Open Source administration and development platform for PostgreSQL, the most advanced Open Source database in the world. 

If you prefer official PostgreSQL client and interface, you can also install and use pgAdmin: https://www.pgadmin.org/download/



If you followed the previous docker configuration (user, password, port number), you can create a new server connection (_object > register > server_) and use the following connection setup:

```{image} ./figures/pgadmin_connection.png
:alt: pgadmin_connection
:width: 500px
:align: center
```

#### <span style="color:darkblue">QGIS<span>

> A Free and Open Source Geographic Information System 

QGIS software is able to interact with a Postgis database and run SQL queries through its powerfull _DB Manager_ plugin. 

To install QGIS, please follow official documentation:
https://www.qgis.org/en/site/forusers/download.html

To add the connection to your local postgis database:
_right clic on "Postgresql" in the "Browser" left menu_ > _new connection_
and use the following setup: 

```{image} ./figures/qgis_connection.png
:alt: qgis_connection
:width: 200px
:align: center
```
