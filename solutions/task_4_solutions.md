# Oppgave 4
1. Skriv en spørring som finner de turene som har lengst reisevei (i luftlinje).
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

2. Skriv en spørring som finner gjennomsnittlig, lengste og korteste tid (`tripduration`), samt summen i tid, brukt på turer mellom samme start og stoppested. Sorter på største sum.
```
SELECT
    start_station_name,
    end_station_name,
    AVG(tripduration) AS mean_duration,
    MAX(tripduration) AS max_duration,
    MIN(tripduration) AS min_duration,
    SUM(tripduration) AS sum_duration,
FROM `<dataset_name>.<view_name>`
GROUP BY start_station_name, end_station_name
ORDER BY sum_duration DESC
```
