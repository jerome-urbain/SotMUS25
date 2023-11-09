# <span style="color:darkblue">Docker stack<span>

---------------
## <span style="color:darkblue">Automated docker stack<span>
---------------
If you want to go further in your deployment, you can directly add the imposm import command at the end of the ```docker-compose.yml``` file: at the end of the stack deployment, docker will also launch the import:

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
    volumes:
        - /home/bde/python_playground/unamur/landmass_identification/datasets/osm/:/import
        - /home/bde/python_playground/unamur/landmass_identification/datasets/osm/mapping/:/mapping
        - ./osm_data:/osm_data
    depends_on:
        - postgis_osm
    deploy: 
        restart_policy:
            condition: on-failure
            delay: 3s
            max_attempts: 5
            window: 5s
    command:  
      /usr/app/imposm import -mapping /mapping/mapping.yml -read /osm_data/andorra-latest.osm.pbf -write -overwritecache -dbschema-import osm -connection postgis://postgres:postgres@postgis_osm:5432/postgres
```


Don't forget to launch a ```docker-compose down``` command if you want to replace a previous setup.

