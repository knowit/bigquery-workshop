# Oppgave 1
3. Gjør en spørring mot `citibike_stations` for å hente ut kapasiteten til hver stasjon, og lagre resultatet fra spørringen i en tabell i datasettet du lagde i oppg. 1.1
```
CREATE [OR REPLACE] TABLE <dataset_name>.<table_name> AS
SELECT station_id, capacity
FROM `bigquery-public-data.new_york_citibike.citibike_stations`
```

4. Gjør en spørring mot `citibike_trips` for å hente ut kun sykkelturer fra 2017-2018 uten feltene `bikeid` og `customer_plan`. Lagre resultatet fra spørringen i en tabell i datsettet ditt.
```
CREATE [OR REPLACE] TABLE <dataset_name>.<table_name> AS
SELECT * EXCEPT(bikeid, customer_plan) 
FROM `bigquery-public-data.new_york_citibike.citibike_trips`
WHERE EXTRACT(YEAR from starttime) in (2017, 2018)
```

5. Gjør en spørring mot de 2 nye tabellene i datasettet ditt og lag et view som joiner kapsiteten sammen med de riktige stasjonene i tabellen med data om sykkelturer. Pass på at du får med kapasiteten til både start stasjonen og slutt stasjonen på turene.
```
WITH t1 AS (
  SELECT trips.*, capacity AS start_station_capacity
  FROM `<dataset_name>.<table_name_from_1.4>` AS trips
  RIGHT JOIN `<dataset_name>.<table_name_from_1.3>` AS stations
  ON trips.start_station_id = stations.station_id
)
 
SELECT t1.*, stations.capacity AS end_station_capacity
FROM t1
RIGHT JOIN `<dataset_name>.<table_name_from_1.3>` AS stations
ON t1.end_station_id = stations.station_id
```
