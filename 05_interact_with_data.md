
### <span style="color:darkblue">Check results stored in Postgis<span>

Depending on your setup, you can browse or query the OSM data which are now stored in Postgis.



#### <span style="color:darkblue">Read table with QGIS<span>
```{note}
NOTE:
Due to the different possible setups tested during the preparation of this workshop, layers/tables/attributes name can differ between the screenshots and what you have on your laptop but the procedure is similar.
```

If you want to read your data stored in Postgis, you can use the following steps in QGIS menus:
Database > DB Manager > Postgis > _[Your_connection_name]_ > _[osm_schema_name]_ > _[osm_table_table]_
> Right clic on _[osm_table_table]_ > Add to canvas

```{image} ./figures/qgis_read_postgis.png
:alt: qgis_read_postgis
:width: 500px
:align: center
```

Your data should now be loaded into QGIS map canvas:

```{image} ./figures/qgis_read_postgis_2.png
:alt: qgis_read_postgis
:width: 500px
:align: center
```
Feel free now to add an OpenStreetMap basemap and play with the properties of the layer!
Why not applying a categorized symbology based on the highway_type attribute for example?

#### <span style="color:darkblue">Browse and query table in DBeaver<span>


```{image} ./figures/dbeaver_browse_data.png
:alt: dbeaver_read_postgis
:width: 500px
:align: center
```


```{warning}

By default these data are stored in "Web Mercator" coordinates system (EPSG 3857).
All the measures are then based on the "mercator meter" unit. If you want more accurate results, don't hesitate to transform the data in a more adequate/relevant projection considering your interest area.
```

- Query 1: 10 longuest roads

```sql
-- top 10 longest roads:
select  round(ST_Length(r.geom)) length, r.*
from osm.osm_road r
order by ST_Length(r.geom) desc 
limit 10
```


- Query 2: number of roads per type

```sql
select count(*), highway_type 
from osm.osm_road r
group by highway_type
order by 1 desc;
```


If you are interested by the proportion of each highway type (based on the number of roads, not their lenght!), you can compute the percentages per type:
```sql
with total as ( select count(*) nb from osm.osm_road r )
select 
	count(r.*) as nb_road, 
	round(100 * count(r.*)::numeric / t.nb::numeric, 2) as percentage, 
	r.highway_type 
from osm.osm_road r, total t
group by r.highway_type, t.nb
order by 1 desc; 
```

- Query 3: km of roads per type
```sql
select r.highway_type, sum(ST_Length(r.geom)) / 1000 as km
from osm.osm_road r
group by r.highway_type
order by 2 desc;
```


