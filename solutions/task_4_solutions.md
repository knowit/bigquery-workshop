# Oppgave 4
1. Finn de turene som bruker lengst tid, og sammenlign dette med hvor lang tid det gjennomsnittlig er brukt på turer med samme start og slutt stasjon.
```
WITH mean_table AS (
   SELECT
     start_station_id,
     end_station_id,
     AVG(tripduration) AS mean
   FROM `<dataset_name>.<view_name>`
   GROUP BY start_station_id, end_station_id
)
 
SELECT tripduration, start_station_name, end_station_name, mean
FROM `<dataset_name>.<view_name>` AS trips
JOIN mean_table
ON trips.start_station_id = mean_table.start_station_id
   AND trips.end_station_id = mean_table.end_station_id
ORDER BY tripduration DESC
```

2. Finn de turene som har lengst reisevei (i luftlinje).
```
WITH t1 AS (
   SELECT
       start_station_name,
       ST_GeogPoint(start_station_longitude, start_station_latitude) AS start_geogpoint,
       end_station_name,
       ST_GeogPoint(end_station_longitude, end_station_latitude) AS end_geogpoint,
   FROM `<dataset_name>.<view_name>`
   GROUP BY start_station_name, start_station_longitude, start_station_latitude, end_station_name, end_station_longitude, end_station_latitude
)

SELECT *, ST_Distance(start_geogpoint, end_geogpoint) AS distance
FROM t1
ORDER BY distance DESC
```
