# <span style="color:#66C3FF">Docker stack<span>

---------------
## <span style="color:#66C3FF">Automated docker stack<span>
---------------
If you want to go further in your deployment, you can directly add the imposm import command at the end of the ```docker-compose.yml``` file: at the end of the stack deployment, docker will also launch the import:

```yaml
version: '3.8'

services:

  postgis_sotm:
    image: postgis/postgis:17-3.5
    ports: 
      - "5482:5432"
    environment:
      - POSTGRES_PASSWORD=postgres
      
  imposm:
    image: geopostcodes/imposm:1.2
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
    command:  
      /usr/app/imposm import -mapping /mapping/mapping.yml -read /osm_data/andorra-latest.osm.pbf -write -overwritecache -dbschema-import osm -connection postgis://postgres:postgres@postgis_sotm:5432/postgres
```


Don't forget to launch a ```docker-compose down``` command if you want to replace a previous setup.

