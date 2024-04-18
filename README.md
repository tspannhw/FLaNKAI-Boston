# FLaNKAI-Boston
Boston, MBTA, Postgresql, NiFi, Kafka, Flink, Iceberg, Data Summit



### Postgresql Calculate Distance

````

select to_char(float8 (point(42.353170,-71.060710) <@> point(stop_lat::float,stop_lon::float)), 'FM999999999.00') as distance,
     stop_name, stop_desc, stop_lat, stop_lon, location_type, stop_url
from mbtalookupstops m
order by to_char(float8 (point(42.353170,-71.060710) <@> point(stop_lat::float,stop_lon::float)), 'FM999999999.00')

````


### References

* https://www.linkedin.com/pulse/lets-calculate-distance-postgresql-jhonatan-garcia/
