# FLaNKAI-Boston
Boston, MBTA, Postgresql, NiFi, Kafka, Flink, Iceberg, Data Summit



### Postgresql Calculate Distance

````

select to_char(float8 (point(42.353170,-71.060710) <@> point(stop_lat::float,stop_lon::float)), 'FM999999999.00') as distance,
     stop_name, stop_desc, stop_lat, stop_lon, location_type, stop_url
from mbtalookupstops m
order by to_char(float8 (point(42.353170,-71.060710) <@> point(stop_lat::float,stop_lon::float)), 'FM999999999.00')

````


### Postgresql Calculate Distance - Function

````
create or replace function location_distance(latitude text, longitude text)
  returns table (distance text, stop_name text, stop_desc text, stop_lat text, stop_lon text, location_type text, stop_url text)
as
$body$     
     select to_char(float8 (point(cast($1 as float),cast($2 as float)) <@> point(stop_lat::float,stop_lon::float)), 'FM999999999.00') as distance,
        stop_name, stop_desc, stop_lat, stop_lon, location_type, stop_url
	 from mbtalookupstops m
     order by to_char(float8 (point(cast($1 as float),cast($2 as float)) <@> point(stop_lat::float,stop_lon::float)), 'FM999999999.00') 
     limit 5

$body$
language sql;

select *
from location_distance('42.353170', '-71.060710')
````

### References

* https://www.linkedin.com/pulse/lets-calculate-distance-postgresql-jhonatan-garcia/
