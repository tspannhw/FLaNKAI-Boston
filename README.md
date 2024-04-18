# FLaNKAI-Boston
Boston, MBTA, Postgresql, NiFi, Kafka, Flink, Iceberg, Data Summit

### Geocode from 2020 US Census

````
https://geocoding.geo.census.gov/geocoder/locations/onelineaddress?address=${location:trim():urlEncode()}&benchmark=2020&format=json
````

### Geocode Maps (requires API Key from https://geocode.maps.co/ MapMaker)

````
https://geocode.maps.co/search?q=${location:trim():urlEncode()}&api_key=APIKEY1234
````

### Address To Lat/Long Python Processor

[https://github.com/tspannhw/FLaNKAI-Boston/blob/main/AddressToLatLong.py](https://github.com/tspannhw/FLaNKAI-Boston/blob/main/AddressToLatLong.py)


````

````
We are using https://geopy.readthedocs.io/en/stable/#nominatim which is a Geocoder for a lot of different libraries.   We are using it to call Nominatim.


[https://openstreetmap.org/copyright](https://openstreetmap.org/copyright)
“OpenStreetMap” a link to openstreetmap.org/copyright,  which has information about OpenStreetMap’s data sources as well as the ODbL.

[https://nominatim.openstreetmap.org/search?q=Lafayette+City+Center+Boston%2C+MA&format=json](https://nominatim.openstreetmap.org/search?q=Lafayette+City+Center+Boston%2C+MA&format=json)

[https://nominatim.openstreetmap.org/ui/search.html](https://nominatim.openstreetmap.org/ui/search.html)
 
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

### Execute SQL against Postgresql Function from NiFi

````
select * from location_distance('${latitude}', '${longitude}')
````

### Slack Reply Template

````
Nearest Buses to ${location} (inside this geofenced box ${boundingbox})
You are currently at a ${displayname} which is a ${addresstype} found at this location @ ${latitude}/${longitude}.
This near by bus stop is ${distance} km(s) away.
It is called ${stopname} @ ${stoplat}/${stoplon}.  [${stopdesc}]
${stopurl}
========Message: ${messagetext} from ${messagerealname} ${messageusername} @ ${messageusertz}
========= Dates: ${date} TS: ${ts} KT: ${kafka.timestamp} 
======== Parsed: Dates: ${dates} Events: ${events} Facs: ${facs} GPE: ${gpes} LOC: ${locs} MONEY: ${moneys}
======== Parsed: ORG: ${orgs} PERSON: ${persons} PRODUCT: ${products} QUANTITY: ${quantities}
=== OSM Details: ${osmclass} ${osmid} ${osmimportance} ${osmlicense} ${osmname} ${osmtype} ${place_id} ${placerank} ${locationtype}
````

### References

* https://www.linkedin.com/pulse/lets-calculate-distance-postgresql-jhonatan-garcia/
