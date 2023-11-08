# <span style="color:darkblue">Postgis<span>

------------
## <span style="color:darkblue">Storing geographic data in Postgresql: Postgis to the rescue!<span>
------------


------------
## <span style="color:darkblue">Interacting with a Postgis database<span>
------------

### <span style="color:darkblue">SQL client<span>


To interact with your database, I recommend 3 clients applications with GUI and SQL editor: DBeaver, pgAdmin or QGIS. You will need at least __one of them__. You could also run SQL command in _psql_ command line tool but it is less user friendly in this learning context. 

#### <span style="color:darkblue">DBeaver community edition<span>
> DBeaver Community is a free cross-platform database tool for developers, database administrators, analysts, and everyone working with data. It supports all popular SQL databases like MySQL, MariaDB, PostgreSQL, SQLite, Apache Family, and more.

DBeaver _community edition_ is the software we recommend to run SQL queries during this workshop. To install it, you can follow the instructions on their official website:
https://dbeaver.io/download/

To create your first PostgreSQL connexion you can follow the documentation: https://dbeaver.com/2022/03/03/how-to-create-database-connection-in-dbeaver/ . 
Note that DBeaver will automatically download and install PostgreSQL driver when you will create your first PostgreSQL connexion. 

#### <span style="color:darkblue">pgAdmin<span>
> pgAdmin is the most popular and feature rich Open Source administration and development platform for PostgreSQL, the most advanced Open Source database in the world. 

If you prefer official PostgreSQL client and interface, you can also install and use pgAdmin: https://www.pgadmin.org/download/


#### <span style="color:darkblue">QGIS<span>

> A Free and Open Source Geographic Information System 

QGIS software is able to interact with a Postgis database and run SQL queries through its powerfull _DB Manager_ plugin. 

To install QGIS, please follow official documentation:
https://www.qgis.org/en/site/forusers/download.html